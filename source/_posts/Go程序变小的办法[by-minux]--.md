---
title: "Go程序变小的办法[by minux]  "
categories: [ "Golang" ]
tags: [ "golang","ldflags" ]
draft: false
slug: "golang-become-smaller"
date: "2014-11-13 08:40:00"
---

strip对Go的程序没啥用的。
`go build -ldflags "-s -w" (go install类似)`


<!--more-->


-s去掉符号表（然后panic时候的stack trace就没有任何文件名/行号信息了，
这个等价于普通C/C++程序被strip的效果），
-w去掉DWARF调试信息，得到的程序就不能用gdb调试了。

比如，server.go是一个简单的http server，用了net/http包。

    $ go build server.go
    $ ls -l server
    -rwxr-xr-x 1 minux staff 4507004 2012-10-25 14:16 server
    $ go build -ldflags "-s -w" server.go 
    $ ls -l server
    -rwxr-xr-x 1 minux staff 2839932 2012-10-25 14:16 server

-s和-w也可以分开使用，一般来说如果不打算用gdb调试，-w基本没啥损失。
-s的损失就有点大了。