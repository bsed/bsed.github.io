---
title: "Windows 下 Vim 插件 Powerline 符号显示解决办法"
categories: [ "Linux" ]
tags: [ "windows","vim","powerline" ]
draft: false
slug: "windows-vim-plugin-powerline-symbol-display-solutions"
date: "2013-04-17 22:12:00"
---

Powerline 是一款非常好用的 Vim Statusbar插件。但是美中不足的是这款插件对windows平台的支持差强人意。笔者在安装的时候就遇到了字体符号显示不正确的问题。辛苦搜索以后找到了解决方案，贴出来共享之。

正常情况下Powerline 的显示效果如下：


<!--more-->


**Powerline-正常模式**

![捕获.png][1]
可以看到上边有一些精致的符号比如箭头啦，最右端的「LN」啦。由于Vim自身的一些局限性这!些特殊的符号只能通过特殊的字符来表示。一般的字体并不会提供相应的字符。如果没有这些特殊符号你看到的 Powerline 应该是这个样子：

**Power-非正常状态**
![捕获1.png][2]
为此 Powerline 自身提供了一个叫做 Fontpatcher 的文件夹来供我们手工修改字体。遗憾的是 Powerline 的作者在文档里只提供了 OS X 与 Linux 平台下字体 patch 的使用方法，却没有提供Windows平台下的解决方法，网上也没有太多资料。搜索了一晚终于让我找到了解决方法。

首先感谢 [codejury](http://www.codejury.com/consolas-font-in-vim-powerline-windows/) 的作者，我从他的一片博文中找到了解决方案。从[这里](https://github.com/eugeneching/consolas-powerline-vim)下载四个字体文件，并安装到操作系统中。

然后在你的 vimrc 文件中添加：
Powerline 配置
Vim

    let g:Powerline_symbols = 'fancy'
    set guifont=Consolas\ for\ Powerline\ FixedD:h11
    set encoding=utf-8

然后就OK了。
可惜在ubuntu15.04 效果不明显啊

  [1]: https://imgs.gnux.cn/usr/uploads/2015/07/945347502.png
  [2]: https://imgs.gnux.cn/usr/uploads/2015/07/353741374.png