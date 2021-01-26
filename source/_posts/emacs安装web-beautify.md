---
title: "emacs安装web-beautify"
categories: [ "Css" ]
tags: [ "emacs" ]
draft: false
slug: "emacs-installation-webbeautify"
date: "2015-08-04 18:25:00"
---

![emacs_start.png][1]
# 1. 安装MELPA
上[http://melpa.org/#/getting-started](http://melpa.org/#/getting-started)后把代码片段加到.emacs文件中去。重新加载Emacs。(如果是在emacs中编辑.emacs，可以直接`M-x`, 输入`eval-buffer`。即可加载最新的配置)

# 2.使用MELPA安装web-beautify
安装成功后，M-x，输入package-list-packages,会列出所有的包,C-s搜索web-beautify,光标选中，回车，在出来的页面里将光标定位到install这个按钮，回车安装。

# 3.配置快捷键
安装成功后，出来的页面会有一段代码，把这段代码复制贴到.emacs中去，重新加载配置文件即可。


<!--more-->


## 附

1.如果是在windows上，以上文中的.emacs都应该是init.el
2.前面第3步那段代码如果没找到或者不小心关掉了，可以到这里去找：[https://github.com/yasuyk/web-beautify](https://github.com/yasuyk/web-beautify)


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/1073575278.png