---
title: "Windows环境下基于sublime text3的node.js开发环境搭建"
categories: [ "JS" ]
tags: [ "nodejs","Sublime Text3" ]
draft: false
slug: "nodejs-development-environment-based-on-text3-windows-in-sublime-environment"
date: "2013-10-16 09:48:00"
---

首先安装sublime text3，百度一堆，自己找吧。理论上sublime text2应该也可以。我只能说一句：这个软件实在是太强悍了。

跨平台，丰富的插件体系，加上插件基本上就是一个强悍的ide了。目前我在使用的主要是Emmet、Python、还有一些格式化的插件（xml，json），加上这次安装的node.js。

node.js的安装就不用多说了，直接 [node.js的安装就不用多说了，直接 [http://nodejs.org/](http://nodejs.org/) 点击install下载window版本的安装程序后安装即可。默认的安装会将安装目录加到path环境变量中，这一步是为了后续使用node的时候，可以不用带路径，当然，后续在sublime text中安装好nodejs插件之后，也可以对node的路径进行一些自定义的设置。为了使用方便，建议增加到path环境变量。 点击install下载window版本的安装程序后安装即可。默认的安装会将安装目录加到path环境变量中，这一步是为了后续使用node的时候，可以不用带路径，当然，后续在sublime text中安装好nodejs插件之后，也可以对node的路径进行一些自定义的设置。为了使用方便，建议增加到path环境变量。

**SublimeText-Nodejs**插件（[https://github.com/tanepiper/SublimeText-Nodejs](https://github.com/tanepiper/SublimeText-Nodejs)）

原本在 sublime text 的插件库里面有 nodejs 的插件，但是经过尝试安装后，发现无法修改编译设置，没找到 `Nodejs.sublime-build` 文件，后来在github上面看了下人家的安装说明之后再重新安装的。


<!--more-->


## 安装也有二种方式：

### 1、直接下载压缩包后解压到sublime text的package目录中。查看package目录在哪可以通过菜单栏中的Preferences-->浏览程序包Browse Packages直接打开package目录。

### 2、使用git命令下载到package目录（`git clone https://github.com/tanepiper/SublimeText-Nodejs "D:\ProgramFiles\Sublime Text 3\Data\Packages\nodejs"`）

修改编译选项，在package目录下的nodejs目录中，打开Nodejs.sublime-build，原始内容如下：

```
{
  "cmd": ["node", "$file"],
  "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
  "selector": "source.js",
  "shell":true,
  "encoding": "cp1252",
  "windows":
    {
      "cmd": ["taskkill /F /IM node.exe & node", "$file"]
    },
  "linux":
    {
        "cmd": ["killall node; node", "$file"]
    }
}

```

有2个地方需要修改，一个是编码，为了避免乱码code，需要改成cp936；另外一个是cmd命令，本身如果只是想简单的运行nodejs程序的话，windows下面的cmd可以直接 "cmd": ["node", "$file"]，但是这样非常不利于开发环境，因为这样的话每次build都会重新启动一个node.exe进程，且会占用一个端口，这肯定是我们不希望的。上文中的cmd原本是想在启动 `node.exe` 之前讲node.exe进程都杀掉，然后再启动node.exe，但是这个命令写的不对，直接使用的话是编译不成功的。对cmd命令需要做简单的处理，修改好之后的 `Nodejs.sublime-build` 文件内容如下：

```json 
{
  "cmd": ["node", "$file"],
  "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
  "selector": "source.js",
  "shell":true,
  "encoding": "cp936",
  "windows":
    {
        "cmd": ["taskkill","/F", "/IM", "node.exe","&","node", "$file"]  
    },

  "linux":
    {
        "cmd": ["killall node; node", "$file"]
    }
}
```

要用sublime打开文件“Nodejs.sublime-settings” 或者 设置“preference” --》 “package settings” --》 “Nodejs”  --》“setting-default” 打开文件并 更改成：

    {
     // save before running commands
     "save_first": true,
     // if present, use this command instead of plain "node"
     // e.g. "/usr/bin/node" or "C:\bin\node.exe"
     "node_command": /usr/bin/nodejs,
     // Same for NPM command
     "npm_command": /usr/bin/npm,
     // as 'NODE_PATH' environment variable for node runtime
     "node_path": false,
     
     "expert_mode": false,
     
     "ouput_to_new_tab": false
    }


重启sublime text之后，配置就算完成了。我们写一小段代码来验证一下是否可以正常运行。

```json
var http = require('http');
var os = require('os');
http.createServer(function (request, response) {
  response.writeHead(200, {'Content-Type': 'text/plain'});
  response.end('Hello World\n');
}).listen(3000);
console.log('Server running at http://127.0.0.1:3000/');
```
Ctrl+b编译这段代码之后，sublime text窗口中就会显示

    Server running at http://127.0.0.1:3000/

若之前有运行的node进程在，则会先杀掉node进程，再启动node，显示如下：

成功: 已终止进程 "node.exe"，其 PID 为 154588。

    Server running at http://127.0.0.1:3000/

到此，服务端算是启动成功，打开浏览器，输入http://127.0.0.1:3000/，页面显示Hello World则表示交互正常。

开发环境就算是基本搭建完毕了，准备接下来一篇文章讲讲怎么使用自带的NPM包管理工具，NPM能解决nodejs代码部署上的很多问题。
 

 



