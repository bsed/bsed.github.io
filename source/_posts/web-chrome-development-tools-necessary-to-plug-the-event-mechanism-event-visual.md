---
title: Chrome Web开发工具必备插件用法之事件机制 Visual Event
date: 2015-07-30 11:34:00
updated: 2015-08-03 16:33:48
tags: 
- chrome
- debug
- plugins
- CSS-Viewer
categories: 
- css

---
> 在html开发中 怎样查看html元素上绑定的事件和方法呢？

WEB标准提倡结构、表现和行为相 分离，现在越来越多采用这种表现和行为的方式，但它也为我们开发调试带来一些问题，网页载入一堆JavaScript，，我们很难搞清楚最后在哪些元素的哪个动作绑定了事件，尤其是JavaScript加载事件的方式五花八门，可以透过`jQuery`、`element.click = function() { }`、`element.addEventListener()`…，很难由单一处找出所有事件。而理不清事件来龙去脉，要追踪某个点击动作背后的行为就变得有些困难，直到我们遇到以下两种利器。

chrome开发者工具有查看HTML元素绑定事件的功能，如下图所示：
[![20150730114000.png][1]](https://imgs.gnux.cn/usr/uploads/2015/07/3486571463.png)


<!--more-->


但这种方式查看事件的方法还是有点困难，直到遇到它chrome的插件 Chrome Web Store - [Visual Event](https://chrome.google.com/webstore/detail/visual-event/pbmmieigblcbldgdokdjpioljjninaim?hl=zh-CN)
`Visual Event`的运作原理，在于其熟知主要JavaScript库(例如: jQuery、YUI、ExtJS)事件机制，可深入其中获取事件，并将其标注在对象元素上。目前支持的JS库包括:

      DOM 0 events
      jQuery 1.2+
      YUI 2
      MooTools 1.2+
      Prototype 1.6+
      Glow
      ExtJS 4.0.x

在启`用Visual Event`后，有绑定事件元素将被标上**蓝色区块**，滑鼠停留时会显示事件的细节。如下图所示：
[![20150730114427.png][2]](https://imgs.gnux.cn/usr/uploads/2015/07/383154516.png)

**官方网址**：[http://www.sprymedia.co.uk/article/Visual+Event+2](http://www.sprymedia.co.uk/article/Visual+Event+2)
  [1]: https://imgs.gnux.cn/usr/uploads/2015/07/3486571463.png
  [2]: https://imgs.gnux.cn/usr/uploads/2015/07/383154516.png