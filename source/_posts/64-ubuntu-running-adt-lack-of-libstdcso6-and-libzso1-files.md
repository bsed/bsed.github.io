---
title:  64位ubuntu运行adt缺少libstdc++.so.6和libz.so.1文件[android] 
date: 2015-01-13 09:22:00
updated: 2015-07-04 09:27:28
tags: 
- linux
- top
categories: 
- linux

---
今天试着在ubuntu上安装了ADT,安装完后才发现，出了问题，在stackoverflow 上找了半天才解决了问题。现总结下来，方便以后翻阅。

系统：ubuntu 15.04 64位
ADT版本：adt-bundle-linux-x86_64-20140702.zip


<!--more-->


## 错误1：

     adb: error while loading shared libraries: libstdc++.so.6: cannot open shared object file: No such file or directory
        ‘adb version’ failed!
        Failed to parse the output of ‘adb version’:

**解决方法：**
 ` sudo apt-get install lib32stdc++6`

## 错误2：

    adt-bundle-linux-x86_64-20140702/sdk/build-tools/android-4.4W/aapt: error while loading shared libraries: libz.so.1: cannot open shared object file: No such file or directory

**解决方法：**
`sudo apt-get install lib32z1`

