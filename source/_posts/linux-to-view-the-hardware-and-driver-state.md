---
title: linux下查看硬件和驱动状态
date: 2014-04-14 09:02:00
updated: 2015-11-07 09:04:11
tags: 
- javascript
- array
- sort
categories: 
- js

---
一直以为在linux下没有像“windows设备管理器”这样直观查看硬件状态和驱动情况的软件，直到给新手提装显卡驱动时，发现了一个叫lshw 的命令。lshw（Hardware Lister）是一个能查看机器详细硬件信息的小软件，它能准确的报告硬件的内存信息、固件版本、主板配置、缓存信息、总线速度等等，支持linux2.4+、powerPC、x86架构的机器（以上简介来自官网，英文烂翻译可能不准）。

在维护硬件时可以用lspci查看哪些硬件连接到了电脑，更详细的信息就可以用lshw来查看，安装就不说了，常见的系统包管理里都有，不常见的还可以源码编译。直接在命令行下敲lshw，就会输出所有的信息。
如果哪个硬件没有驱动，lshw输出时会有UNCLAIMED这类的字样。例子就不给了，输出内容太多了。


<!--more-->


    lshw参数:
    
     -help 查看帮助
     -version  打印版本
     -X GUI显示，要装lshw-gtk 或者lshw-gui
    
    输出的格式
    -html          以HTML格式输出
    -xml           以XML 格式输出
    -short         显示设备列表，输出包括设备路径(path)、类别(class)以及简单描述
    -businfo       输出包括总线信息、SCSI、USB、IDE、PCI地址等信息
    
    选项：
    -class CLASS    显示指定类别的设备
    -C CLASS        同上
    -c CLASS        同上
    -disable TEST   不检测信息 像 pci 、isapnp、 cpuid等等
    -enable TEST    与上面相反，启用检测   
    -quiet          直接输出结果，不显示检测过程
    -sanitize       无害输出 (会移除像硬件序列号等一些敏感信息)
    -numeric        显示硬件id (PCI、USB等)

参考文章：
[http://blog.chinaunix.net/uid-10697776-id-3080243.html](http://blog.chinaunix.net/uid-10697776-id-3080243.html)
[http://jiajun.iteye.com/blog/656709](http://jiajun.iteye.com/blog/656709)

