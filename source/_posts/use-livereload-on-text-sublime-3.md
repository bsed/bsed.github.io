---
title: 在Sublime Text 3上使用LiveReload
date: 2015-11-01 15:39:00
updated: 2016-01-24 08:13:45
tags: 
- linux
- rm--rf
categories: 
- linux

---
![pcar.png][1]
## 安装 Live Reload
Ctrl + Shift + p 输入　PCAR (Package Control: Add Repository ) 在　在Package 上添加　仓库

`https://raw.github.com/Grafikart/ST3-LiveReload/master/package.json`

Ctrl + Shift + p 输入 PCIP (Package Control: Install Package ) 搜索 Live Reload
如图：


<!--more-->


![pcip.png][2]

Preferences > Package Settings > LiveReload > Setting - Default

    { 
    	"enabled_plugins": [ 
    		"SimpleReloadPlugin", 
    		"SimpleRefresh" 
    	]
    }

使用：
![prefpc.png][3]
Chrome 插件：
    [https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei](https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei)

Firefox插件:
    [https://addons.mozilla.org/fr/firefox/addon/livereload/](https://addons.mozilla.org/fr/firefox/addon/livereload/)

详细使用看原文：
http://feedback.livereload.com/knowledgebase/articles/86242-how-do-i-install-and-use-the-browser-extensions

[http://anthozano.fr/livereload-pour-sublime-text-3/](http://anthozano.fr/livereload-pour-sublime-text-3/)


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/1680462765.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/01/3279424210.png
  [3]: https://imgs.gnux.cn/usr/uploads/2016/01/2617777531.png