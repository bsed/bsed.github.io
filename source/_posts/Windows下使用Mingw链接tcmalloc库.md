---
title: "Windows下使用Mingw链接tcmalloc库"
categories: [ "Vim" ]
tags: [ "tcmalloc" ]
draft: false
slug: "windows-mingw-link-tcmalloc"
date: "2014-12-29 17:56:00"
---

  tcmalloc库本文不详细介绍，见如下链接。
http://goog-perftools.sourceforge.net/doc/tcmalloc.html
        关于在Windows下使用tcmalloc库，都是讲如何在msvc环境下链接tcmalloc库，如下链接所示。
http://hi.baidu.com/widebright/item/21e168db21bbf5866dce3f01
        本文介绍如何使用mingw链接tcmalloc库，也算是一个补充。


<!--more-->


首先要用msvc编译出.lib和.dll文件，tcmalloc库源码里面有.sln文件，用vc 2010打开就可以编译了。编译出的.lib文件和.dll文件不能直接使用，需要进行处理。
先用pexports工具生成dll文件的def文件

    D:\Program Files\Dev-Cpp\MinGW32\lib>pexports libtcmalloc_minimal.dll > tcmalloc.def
    D:\Program Files\Dev-Cpp\MinGW32\lib>

再用dlltool生成.a文件

    D:\Program Files\Dev-Cpp\MinGW32\lib>dlltool -d tcmalloc.def -D libtcmalloc_minimal -l libtcmalloc_minimal.a
    D:\Program Files\Dev-Cpp\MinGW32\lib>

生成的.a文件放到mingw的lib路径下，libtcmalloc_minimal.dll 放到C:\WINDOWS\system32就可以了。
在使用的时候，gcc不仅需要链接libtcmalloc_minimal.a文件，还需要强制引用__tcmalloc符号，如下所示

    E:\>gcc  -g -ltcmalloc_minimal -u __tcmalloc -o a.exe a.c
    E:\>

不然链接器会直接丢弃tcmalloc的所有符号，因为编译器发现用不到这些符号。