---
title: "HTML5 视频直播（二）［转］"
categories: [ "Css" ]
tags: [ "html5","video" ]
draft: false
slug: "html5-video-broadcast-two"
date: "2015-09-20 21:29:00"
---

[上篇博客](https://yigrherb.com/2015/09/20/html5-video-broadcast-a-transfer.html)中，我介绍了目前移动端唯一可行的 HTML5 直播方案：HLS。实际上，HLS 除了上回提到过的延迟很大这个缺点之外，在 iOS 的 Safari 浏览器中还只能全屏播放，也无法做到自动播放，这个是 iOS 系统对 Video 标签统一做的限制。有没有什么办法解决这些问题呢？

我们换个思路，既然原生 Video 有这样那样的问题，不如直接抛弃它。利用 Web Sockets 实现视频流的实时传输，使用纯 JS 进行视频解码，再用 Canvas 逐帧画出图像，这不就实现了直播么。当我有个这个想法之后，初步觉得可行，立马开始一番搜索，收获颇丰。

本文要用到的 Web Sockets 在移动端支持度如下表：
| 浏览器 	| 支持新标准的版本 |
| -- | -- |
| iOS Safari | 6.1+ | 
| Android Browser（Webview） | 4.4+ | 
| Chrome for Android | 42+ |

（数据来源：[caniuse](http://caniuse.com/#search=websockets)）

另外，转换视频格式、生成视频流还需要用到一个神器：[FFmepg]http://ffmpeg.org/()。

Mac 下最简单的做法是通过 [Homebrew](http://brew.sh/) 安装，直接` brew install ffmpeg` 就可以了。Ubuntu 下可以先添加这个源：

    sudo add-apt-repository ppa:mc3man/trusty-media

## Decoder in JavaScript

纯 JS 实现的视频解码器我找到了两个可用的：[Broadway](https://github.com/mbebenita/Broadway) 和 [jsmpeg](https://github.com/phoboslab/jsmpeg)。

Broadway 是一个 H.264 解码器，使用 Emscripten 工具从 Android 的 H.264 解码器转化而成，它还针对 WebGL 做了一些优化。

这个解码器支持 mp4 后缀的视频文件，有一些限制：不支持 weighted prediction for P-frames 和 CABAC entropy encoding。例如 iPhone 拍摄的视频它就不支持，可以用 FFmpeg 转一下：

    ffmpeg -i in.mp4 -vcodec libx264 -pass 1 -coder 0 -bf 0 -flags -loop -wpredp 0 out.m

下面是 H.264 解码示例，视频来自于我的 iPhone 拍摄。

这里还有一个长一点的 [Demo](http://qgy18.imququ.com/jsvideo/H.264/)，点击查看（加载完 6M 多的 mp4 文件才开始播放，请耐心等待，流量党慎入）。

jsmpeg 则是一个 MPEG1 解码器，它是由作者从头编写出来的，并不像 Broadway 那样是从其他语言翻译而成，所以代码可读性要好很多，代码也更轻量级。

jsmpeg 也对视频文件编码方式有一些要求：不支持 B-Frames，视频宽度必须是 2 的倍数。还是可以用 FFmpeg 来转换：

    ffmpeg -i in.mp4 -f mpeg1video -vf "crop=iw-mod(iw\,2):ih-mod(ih\,2)" -b 0 out.mpg

下面是 MPEG1 解码示例，视频来自于网上
这里也有一个长一点的 Demo，点击查看（加载完 3M 多的 mpg 文件才开始播放。其实没什么好看的，内容跟上面一样，编码格式不同而已）。

### Live Streaming

看到这里，大家肯定会说，这不是要一次性下完全部内容么，怎能称之为直播。是的，要实现直播，还要用 Web Sockets 实现一个实时传输流的服务。FFmpeg 支持很多直播流格式，但不支持 Web Sockets。解决方案是用 FFmpeg 开一个 HTTP 直播流，再开个 Node 服务转一下。

详细一点的过程是这样的，用 NodeJS 监听 FFmpeg 的 HTTP 直播地址，把收到的数据 通过 Web Sockets 广播给所有客户端。核心代码就是下面这几行：

*JS*

    //HTTP Server to accept incomming MPEG Stream
    var streamServer = require('http').createServer( function(request, response) {
        request.on('data', function(data){
            socketServer.broadcast(data, {binary:true});
        });
    }).listen(STREAM_PORT);

这段代码来自于上面介绍的 jsmpeg 项目，完整代码在这里。启动这个服务试试（需先装好 ws 模块）：

    node ~/live/stream-server.js ququ 9091 9092

三个参数分别是加密串、HTTP 端口、WS 端口。启动后，屏幕上会显示两个地址：

    Listening for MPEG Stream on http://127.0.0.1:9091/<secret>/<width>/<height>
    Awaiting WebSocket connections on ws://127.0.0.1:9092/

好了，现在就可以使用 FFmpeg 来推送 HTTP 视频流了：

    ffmpeg -re -i fox.mpg -codec copy -f mpeg1video http://qgy18.imququ.com:9091/ququ/640/360

`-re` 参数表示以视频实际播放速率解码，不加这个参数一般会导致直播速率变得飞快。`ququ` 是启动 `Node` 服务时指定的加密串，这样做个简单校验，避免 Node 转发不认识的流。最后的 `640` 和 `360` 是视频的宽高，可以根据实际情况指定。

最后，稍微改一下前面的 Demo，让 jsmpeg 从 WS 流中获取数据就可以实现直播了：

    var canvas = document.getElementById('videoCanvas');
    var client = new WebSocket('ws://qgy18.imququ.com:9092/');
    var player = new jsmpeg(client, {canvas:canvas, autoplay: true});

（完整示例地址）

### Capture Webcam

上面演示的是从文件中获取视频流进行直播，如果把数据源换成摄像头也很容易。FFmpeg 官方 wiki 上有在 Windows / MacOS / Linux 下读取摄像头的详细指南。

以 Mac 为例，它支持 AVFoundation 和 QTKit 两种不同的技术读取摄像头，在比较新的系统（10.7+）上，推荐使用 AVFoundation，QTKit 后续可能会被废弃。

我的 Mac 系统是最新的，直接使用 AVFoundation。首先查看可用摄像头列表：

*SHELL:*

    ffmpeg -f avfoundation -list_devices true -i ""
    
    [AVFoundation input device @ 0x7fdd53c228c0] AVFoundation video devices:
    [AVFoundation input device @ 0x7fdd53c228c0] [0] FaceTime HD Camera
    [AVFoundation input device @ 0x7fdd53c228c0] [1] Capture screen 0
    [AVFoundation input device @ 0x7fdd53c228c0] AVFoundation audio devices:
    [AVFoundation input device @ 0x7fdd53c228c0] [0] Built-in Microphone

可以看到，编号为 0 的 video 设备正是我想要的摄像头（编号为 1 的设备是电脑屏幕，直播屏幕也挺好玩），下面这行命令就可以捕捉它，并把视频流推到之前的 Node 服务上：

    ffmpeg -f avfoundation -i "0" -f mpeg1video -b 500k -r 20 -vf scale=640:360 http://qgy18.imququ.com:9091/ququ/640/360

-b 和 -r 分别用来指定码率和帧率，可以根据实际情况调整。这样，我摄像头拍摄到的画面，就被 FFMpeg 捕捉下来实时推给远端 Node 服务，实时转化成 WS 数据流，广播给所有终端播放。虽然过程很曲折，但是基本上看不到延迟，体验还是很不错的。

最后，说几个问题：

    首先，最大的问题是：这种方案只实现了 Canvas 渲染画面部分，无法支持声音（没声音还好意思叫直播，摔！！！）；
    其次，JS 解码能力还是稍微有点弱，最后的示例中我有意指定 scale 参数缩小了画面，但在 iPhone 6 Plus 非自带浏览器中还会卡（iOS 第三方 APP 赶紧放弃老系统，果断的把好内核用起来吧~~~）；
    第三，好像略微有点蛋疼（好，这次就写这么多，收工。。。）；

原文链接：[https://imququ.com/post/html5-live-player-2.html](https://imququ.com/post/html5-live-player-2.html)

