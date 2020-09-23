---
title: Atom中文显示乱码问题 UTF-8 
date: 2015-09-06 13:53:00
updated: 2016-06-02 11:11:08
tags: 
- restful
categories: 
- model

---
Atom中文显示乱码问题 本来就是UTF-8文件，但中文还是显示不了。换了GBK、GB18030也不行。
解决方法： 给atom编辑器设置字体

从菜单中打开Packages->Setting views->Open，

 找到config.cson文件中的editor子项，或者setting views中的font-family选项，把字体设置成 文泉驿 系列字体才可以显示中文。比如：


<!--more-->


文泉驿微米黑,文泉驛微米黑,`WenQuanYi Micro Hei`

`文泉驿等宽微米黑,文泉驛等寬微米黑,WenQuanYi Micro Hei Mono`

`文泉驿正黑`


等字体。注意：先查看操作系统是否安装了这些字体，打开终端输入： `fc-list :lang=zh` 命令查看安装的字体。

`root@gnux:~$ fc-list :lang=zh`

例如，我的配置如下：

    "*":
      welcome:
        showOnStartup: false
      minimap:
        plugins:
          "find-and-replace": true
      core:
        disabledPackages: [
          "symbols-view"
        ]
        themes: [
          "atom-solarized-dark-ui"
          "solarized-dark-syntax"
        ]
      editor:
        fontSize: 16
        invisibles: {}
        tabLength: 4
        fontFamily: "WenQuanYi Micro Hei Mono"
