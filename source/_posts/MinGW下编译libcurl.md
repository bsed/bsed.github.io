---
title: "MinGW下编译libcurl"
categories: [ "日常" ]
tags: [ "libcurl","mingw" ]
draft: false
slug: "compiled-under-mingw-libcurl"
date: "2012-10-31 14:17:00"
---

libcurl 是鼎鼎大名的开源客户端 URL 传输库，支持 FTP、HTTP 以及其它很多乱七八糟的协议。在各种语言上的实现也很多：C、C++、Lua、Java、Pascal、Perl、PHP、Python、Ruby、Visual Basic……。这里说最常用的 C 语言实现，环境是 Windows 7 + MinGW32。

##  下载 libcurl 源代码  ##

libcurl 可以在官网 http://curl.haxx.se/ 获得。Download 页有源代码和为各平台预编译的二进制文件（curl 程序）和开发包（include + lib + doc）下载。不过我想要的 MinGW32 开发包的链接失效了 = = 就下载个源代码包自己编译吧。


<!--more-->


在 Download 页的 Source Archives 栏里有最新的 curl 7.27.0 版本源代码压缩包，下载 curl-7.27.0.zip 文件。

##  编译 libcurl 库  ##

 1. 解压 curl-7.27.0.zip 文件
 2. 通过命令提示符进入 curl-7.27.0 文件夹
 3. 输入 mingw32-make mingw32 进行生成（这里我只需要普通的功能，于是没有加附加的选项）

编译完成后，在 lib 文件夹中会有我们需要的三个文件。

 - `libcurl.a` 静态链接库
 - `libcurldll.a` 动态链接库的导入库
 - `libcurl.dll` 动态链接库

接下来，可以进入 docs/examples 文件夹，测试编译一些示例程序。可以直接用 Makefile.m32 文件，也可以手动一个个用 gcc 编译。

动态链接：

    gcc -I../../include -L../../lib simple.c -lcurldll 

静态链接：

    gcc -I../../include -L../../lib simple.c -DCURL_STATICLIB -lcurl -lws2_32 -lwldap32 

动态链接编译出的程序运行时依赖 libcurl.dll。静态链接参数中的 ws2_32 是 Windows Socket 2 库，wldap32 是微软的 Lightweight Directory Access Protocol API 库。

##  一般使用流程  ##
基本的 curl 程序主要分四步：

 1. curl_easy_init 创建 CURL 对象
 2. curl_easy_setopt 设置操作选项
 3. curl_easy_perform 进行操作
 4. curl_easy_cleanup 销毁 CURL 对象

最简单的示例可以看 doc/examples 下的 simple.c 文件，演示如何将 curl 主页内容输出到 stdout。