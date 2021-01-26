---
title: "mingw/msys中安装pkg-config"
categories: [ "Linux" ]
tags: [ "mingw","pkg-config" ]
draft: false
slug: "install-pkgconfig-mingwmsys"
date: "2015-01-23 14:45:00"
---

在编译vmime的时候，发现configure的时候老是提示gnutls库找不到，这个明明已经是安装了的。稍微看了下configure文件之后，发现是因为pkg-config没有安装。

按照mingw的faq的提示，去[这里](http://www.gtk.org/download/win32.php)下载了glib和pkg-config的二进制包。解压缩glib的包，将里面的libglib.dll复制到/mingw/bin中，然后将pkg-config.exe也复制到/mingw/bin中。再次运行的时候又提示pkg-config.exe缺少intl.dll，仔细看了下载页面，这个dll是在gettext的二进制包里面的，下载之。释放里面的intl.dll到/mingw/bin之后，pgk-config就能够正常运行了，vmime也能够正常编译了。

*注意:*
glib包中实际上只用到libglib-2.0-0.dll  就可以了