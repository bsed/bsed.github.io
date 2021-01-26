---
title: "Linux下GCC编译器用于编译运行的内核版本"
categories: [ "Linux" ]
tags: [ "gcc" ]
draft: false
slug: "linux-under-the-gcc-compiler-used-to-compile-the-running-kernel-version"
date: "2015-01-13 18:33:00"
---

我的系统上安装有gcc 的多个版本

    ls -l /usr/bin/gcc*
    lrwxrwxrwx 1 root root 7 Jan 11 05:20 /usr/bin/gcc -> gcc-4.4
    -rwxr-xr-x 1 root root 239528 Sep 21 2010 /usr/bin/gcc-4.3
    -rwxr-xr-x 1 root root 255536 Nov 15 2010 /usr/bin/gcc-4.4

我需要使用GNU的编译器版本相同的NVIDIA驱动程序的建立是用来编译当前运行的内核。我如何才能找到我的GNU GCC编译器的版本


<!--more-->


你需要`cat /proc/version`文件。该文件确定正在运行的内核版本和版本的GCC用于编译内核。
从Debian6/Linux输出的例子

打开一个命令行终端（选择应用程序>附件>终端），然后输入：

    $ cat /proc/version

输出：
linux version 2.6.32-5-amd64 (Debian 2.6.32-41) (ben@decadent.org.uk) (gcc version 4.3.5 (Debian 4.3.5-4) ) #1 SMP Mon Jan 16 16:22:28 UTC 2014

(Fig.01: This kernel was built using gcc version 4.3.5)

从 CentOS / RHEL 6.x Server输出的例子

输入下面的命令：

    $ cat /proc/version

输出：
Linux version 2.6.32-220.2.1.el6.x86_64 (mockbuild@x86-009.build.bos.redhat.com) (gcc version 4.4.6 20110731 (Red Hat 4.4.6-3) (GCC) ) #1 SMP Tue Dec 13 16:21:34 EST 2013

(Fig.02: This kernel was built using gcc version 4.4.6)

如何部分使用编译器？

你需要设置环境变量 CC 称为指向您的C编译器。在本例中设置CC GCC版本4.3。x：

    $ export CC=/usr/bin/gcc-4.3
    $ make

或者用 NVIDIA module的gcc-4.3:

    $ export CC=/usr/bin/gcc-4.3
    $ bash NVIDIA-Linux-x86_64-295.20.run