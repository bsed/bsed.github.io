---
title: Ubuntu 17.10下编译安装Wiznote
date: 2017-07-06 21:04:00
updated: 2017-07-07 20:59:53
tags: 
- javascript
- constructors
categories: 
- js

---
为知笔记是基于qt 构建的，所以跨平台，也在Linux有客户端，而且使用它的确很方便！
最近在Debian 8下源码编译了Wiznote，虽然简单，但也遇到了点问题，所以记录一下。

## 安装依赖包
```bash
sudo apt-get install build-essential  cmake  libqt4-dev   zlib1g-dev
```

## 下载源码

源码托管在Github，地址：https://github.com/wizteam/wizqtclient

## 编译安装

cd   源码目录/build
```bash
cmake ..
make
sudo make install
```


<!--more-->


## Ubuntu 17.10下遇到的问题

在Ubuntu 17.10 下，为知笔记自带的那个cryptopp在编译的时候会出现问题，lcryptlib链接会出现问题。

需要用#注释 lib/CMakeLists.txt中 “ add_subdirectory(cryptopp) ”这一行，然后把 src/CMakeLists.txt 中最底部的 lcryptlib替换为cryptopp。

然后就OK了！


## 1.无法输入中文

开始时使用Qt5.8编译，按照 https://github.com/WizTeam/WizQTClient/issues/423 中建议，拷贝了相应的动态库，重新编译后可以在搜索框和笔记的标题栏输入中文了，但编辑区还是不行。又看了https://github.com/WizTeam/WizQTClient/issues/443 这一个Issue，说明可能是上游的Qt出了问题，这个问题在5.9版本得到了修复。

解决方法：重新安装了Qt5.7，重新按照 https://github.com/WizTeam/WizQTClient/issues/423 拷贝了动态库（忘了5.9版本是不是必须了，反正我拷贝后是可以的），然后编译运行，成功输入中文。

 

## 2.无法点击系统的图标打开WizNote

通过 make 和 sudo make install 安装完成后，可能无法打开程序。即 @750145113 童鞋所说，会遇到 “error while loading shared libraries: libQt5WebEngine.so.5: cannot open shared object file: No such file or directory” 的错误。这是因为程序运行时找不到Qt相关的动态链接库导致的。所以要把Qt相关链接库的路径告诉程序。

解决方法：在` /etc/ld.so.conf.d/` 文件夹下新建 wiznote.conf 文件，写入Qt的动态链接库路径，我的是 `/home/kelvin/Qt5.7/5.7/gcc_64/lib`，保存后，在终端执行 `sudo ldconfig`。

如图：
