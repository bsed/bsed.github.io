---
title: "Linux下编译安装PCRE库"
categories: [ "Linux" ]
tags: [ "pcre" ]
draft: false
slug: "install-the-pcre-library-under-linux-compiler"
date: "2015-05-01 07:34:00"
---

最近在学习nginx，nginx rewrite依赖于PCRE库，所以需要在linux系统中编译安装PCRE库。具体步骤如下：
 
## 1.下载PCRE包
首先去官网下载pcre的安装包
如果通过FTP的方式，下载地址为：[ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/]ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/()
如果通过http的方式，下载地址为：[http://sourceforge.net/projects/pcre/files/pcre/](http://sourceforge.net/projects/pcre/files/pcre/)
目前最新的版本为8.32，linux对应的安装包名称为：pcre-8.32.tar.gz。
 


<!--more-->


## 2.把安装包上传到服务器，然后解压

    [root@localhost local]# tar -zxvf pcre-8.32.tar.gz  

解压之后在当前目录下生成了一个文件夹：pcre-8.32

`drwxr-xr-x 7 1169  1169    4096 Nov 30 18:50 pcre-8.32  `

## 3.配置
Shell代码

`[root@localhost pcre-8.32]# ./configure  `

    pcre-8.32 configuration summary:
    
        Install prefix .................. : /usr/local
        C preprocessor .................. : gcc -E
        C compiler ...................... : gcc
        C++ preprocessor ................ : g++ -E
        C++ compiler .................... : g++
        Linker .......................... : /usr/bin/ld
        C preprocessor flags ............ : 
        C compiler flags ................ : -O2 -fvisibility=hidden
        C++ compiler flags .............. : -O2 -fvisibility=hidden -fvisibility-inlines-hidden
        Linker flags .................... : 
        Extra libraries ................. : 
    
        Build 8 bit pcre library ........ : yes
        Build 16 bit pcre library ....... : no
        Build 32 bit pcre library ....... : no
        Build C++ library ............... : yes
        Enable JIT compiling support .... : no
        Enable UTF-8/16/32 support ...... : no
        Unicode properties .............. : no
        Newline char/sequence ........... : lf
        \R matches only ANYCRLF ......... : no
        EBCDIC coding ................... : no
        EBCDIC code for NL .............. : n/a
        Rebuild char tables ............. : no
        Use stack recursion ............. : yes
        POSIX mem threshold ............. : 10
        Internal link size .............. : 2
        Match limit ..................... : 10000000
        Match limit recursion ........... : MATCH_LIMIT
        Build shared libs ............... : yes
        Build static libs ............... : yes
        Use JIT in pcregrep ............. : no
        Buffer size for pcregrep ........ : 20480
        Link pcregrep with libz ......... : no
        Link pcregrep with libbz2 ....... : no
        Link pcretest with libedit ...... : no
        Link pcretest with libreadline .. : no
        Valgrind support ................ : no
        Code coverage ................... : no

## 4.编译
执行make操作：

 编译完后可以执行make check进行测试（这一步非必须）
Shell代码

    Testing pcregrep version 8.32 2012-11-30
    Testing pcregrep main features
    Skipping pcregrep UTF-8 tests: no UTF-8 support in PCRE library
    Testing pcregrep newline settings
    PASS: RunGrepTest
    ==================
    All 5 tests passed
    ==================
    make[2]: Leaving directory `/usr/local/pcre-8.32'
    make[1]: Leaving directory `/usr/local/pcre-8.32'
    [root@localhost pcre-8.32]# 

 上面的make check结果中有一句:
Shell代码

    Skipping pcregrep UTF-8 tests: no UTF-8 support in PCRE library  

 这是因为在前面步骤中执行./configuration配置时没有加上对utf-8的支持
如果要加上对utf-8的支持可以在./configuration时加上参数：

Shell代码

    [root@localhost pcre-8.32]# ./configure --enable-utf8

 配置完后再重新make
make完后再次执行make check结果如下：

    Testing pcregrep version 8.32 2012-11-30
    Testing pcregrep main features
    Testing pcregrep UTF-8 features
    Testing pcregrep newline settings
    PASS: RunGrepTest
    ==================
    All 5 tests passed
    ==================
    make[2]: Leaving directory `/usr/local/pcre-8.32'
    make[1]: Leaving directory `/usr/local/pcre-8.32'
    [root@localhost pcre-8.32]#

## 5.安装
执行make install操作：
Shell代码
`[root@localhost pcre-8.32]# make install  `

make install结束后pcre编译安装流程就结束了。