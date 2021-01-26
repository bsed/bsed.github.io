---
title: " 在linux环境中利用efibootmgr管理efi启动项"
categories: [ "Linux" ]
tags: [ "linux","efi","efibootmgr" ]
draft: false
slug: "using-efibootmgr-to-manage-efi-startup-items-in-an-linux-environment"
date: "2017-12-12 12:59:00"
---

UEFI用来替代传统BIOS引导操作系统，学会修改UEFI启动项也变得十分重要，UEFI全称为：“统一的可扩展固件接口”（Unified Extensible Firmware Interface），目前新购入的计算机都支持UEFI固件，而且为了兼容传统BIOS，都会使用CSM（Compatibility Support Module）以兼容。Windows8及以上都能很好的支持UEFI，而为了设置系统引导，必须要管理UEFI启动项。

之前我的电脑为了使用grub2，修改了ESP分区里的默认引导文件，但是没有效果，检查发现EFI启动项里根本没有`/EFI/Boot/bootx64.efi`这项，所以修改默认引导文件仍然直接进入Windows。解决方法就是给EFI启动项添加`bootx64.efi`这项，在Linux环境下可以使用**efibootmgr命**令，而在Windows下可以使用EasyUEFI这个软件。


<!--more-->


在Ubuntu下我使用**efibootmgr**命令，显示如下：
```bash
BootCurrent: 0005 
Timeout: 0 seconds 
BootOrder: 0004,0005,0001,0000,2001,2002,2003 
Boot0000* Realtek PXE B08 D00 
Boot0001* ST500LM021-1KJ152 
Boot0004 Windows Boot Manager 
Boot0005 ubuntu 
Boot2001* EFI USB Device 
Boot2002* EFI DVD/CDROM 
Boot2003* EFI Network
```
由上可知，当前的bootcurrent标号为5，是ubuntu，下面*对应于启动项的有效无效。

列表还是很直观的显示了当前启动项，启动超时，EFI启动顺序，下面是具体的启动项，我的电脑0004是Windows的启动项，0005是ubuntu，0005是我手动添加的，就是默认启动项，原来是没有的，使用命令如下：
```bash
efibootmgr -c -w -L "BootOptionName" -d /dev/sda -p 1 -l \\EFI\\Boot\\bootx64.efi
```
其中BootOptionName替换成你设定的名字，`-d`后面是启动分区所在的硬盘（默认为`/dev/sda`），-p后面是分区位置（默认为1），-l是启动efi文件的路径。
这样就添加了EFI启动项
0004这项应该是Windows自己添加上的，删除后再进入Windows又会出现。
删除启动项的命令为：
```bash
efibootmgr -b 000X -B
```
000X是你想删除的启动项编号。

下面介绍几种常见的操作

**创建一个新的boot option**
efibootmgr -c

**修改boot 顺序** 
efibootmgr -o X,Y #指定标号为X的启动项顺序在Y之前

**启用/禁用boot option** 

efibootmgr -a -b X启用标号为X的启动项 
efibootmgr -A -b X禁用标号为X的启动项

