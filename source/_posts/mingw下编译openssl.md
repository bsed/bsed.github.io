---
title: "mingw下编译openssl"
categories: [ "Linux" ]
tags: [ "mingw","openssl" ]
draft: false
slug: "compiled-under-mingw-openssl"
date: "2014-01-14 13:02:00"
---

对mingw及类linux系统下命令行工具不熟，备忘在此

 目的：在windows xp环境下，编译开源库openssl

环境：windows xp ,Mingw及自带msys工具，openssl-1.0.2.tar.gz


<!--more-->


1.安装mingw(自带msys)

    在 [http://www.mingw.org/](http://www.mingw.org/) 网站下载在线安装包mingw-get-inst-20140426.exe  在线安装好，注意选择c\c++编译器，选择msys
    在  下载  ftp://ftp.openssl.org/source/openssl-1.0.2.tar.gz  下载 openssl-1.0.2.tar.gz 
  安装好的MinGW目录结构如下，注意 D:\MinGW\msys\1.0目录下的 msys.bat

2.启动Mingw的命令行工具msys.bat ，命令行方式，解压缩 openssl-1.0.2.tar.gz 文件  

    命令：     tar -zxvf openssl-1.0.2.tar.gz

   进入解压后的 openssl源代码目录C:\MinGW\msys\1.0\src\  ，如图

3.编译方法见INSTALL  文件，以及INSTALL.WIN32文件

    If you want to just get on with it, do:

  按说明书上，依次执行命令：

    $ ./config  --prefix=/usr/local --openssldir=/usr/local/ssl
    $ make && make install
    $ make test (可有可无)

  默认编译得到的是静态库，而我需要的是动态库,只需添加参数 shared 即可:
  

    $ ./config shared --prefix=/usr/local --openssldir=/usr/local/ssl
    $ make clean
    $ make && make install

下面摘自其 INSTALL 说明文件

Configuration Options
 ---------------------
 There are several options to ./config (or ./Configure) to customize
 the build:         .....

  shared        In addition to the usual static libraries, create shared
                libraries on platforms where it's supported.  See "Note on
                shared libraries" below.

make install完毕之后，会在C:\MinGW\msys\1.0\local\ssl的目录下找到openssl库，包括了头文件，dll文件等

若果编译不同 可以参考 **建议 编译静态动态一共2次**
[http://lamp.linux.gov.cn/OpenSSL/OpenSSL_install_config.html](http://lamp.linux.gov.cn/OpenSSL/OpenSSL_install_config.html)
[https://github.com/zpao/spidernode/wiki/Building-node.js-on-mingw](https://github.com/zpao/spidernode/wiki/Building-node.js-on-mingw)
[http://www.v2ex.com/t/120955](http://www.v2ex.com/t/120955)