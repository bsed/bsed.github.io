---
title: "GRUB2配置详解：默认启动项，超时时间，隐藏引导菜单，配置文件详解，图形化配置"
categories: [ "Linux" ]
tags: [ "linux","bash","grub" ]
draft: false
slug: "grub2-configuration-details-default-startup-items-timeout-hidden-boot-menu-configuration-file-detailed-graphical-configuration"
date: "2014-03-19 19:58:00"
---

配置文件详解: `/etc/default/grub`
```bash
# 设定默认启动项，推荐使用数字
GRUB_DEFAULT=0

# 注释掉下面这行将会显示引导菜单
#GRUB_HIDDEN_TIMEOUT=0

# 黑屏，并且不显示GRUB_HIDDEN_TIMEOUT过程中的倒计时
GRUB_HIDDEN_TIMEOUT_QUIET=true


<!--more-->


# 设定超时时间，默认为10秒
# 设定为-1取消倒计时
GRUB_TIMEOUT=10

# 获得发行版名称（比如Ubuntu, Debian）
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`

# 将会导入到每个启动项（包括recovery mode启动项)的'linux'命令行
GRUB_CMDLINE_LINUX=""

# 同上，但是只会添加到 normal mode 的启动项
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"

# 取消注释以允许图形终端（只适合grub-pc）
#GRUB_TERMINAL=console

# 分辨率设定，否则采用默认值
#GRUB_GFXMODE=640x480

# 取消注释以阻止GRUB将传递参数 "root=UUID=xxx" 传递给 Linux
#GRUB_DISABLE_LINUX_UUID=true

# 取消启动菜单中的“Recovery Mode”选项
#GRUB_DISABLE_LINUX_RECOVERY="true"

# 当GRUB菜单出现时发出鸣音提醒
#GRUB_INIT_TUNE="480 440 1"
```
配置进阶
获得当前GRUB2有效的启动项
```bash
$ grep menuentry /boot/grub/grub.cfg
```
配置默认启动项
```bash
GRUB_DEFAULT=x
```
注释：x为数字，以指定启动列表中第x项为启动项，x从0开始计数
```
GRUB_DEFAULT="xxxx"
```
注释：指出具体的启动项名称，例如`GRUB_DEFAULT="Ubuntu, Linux 2.6.31-9-generic"`
```bash

 1. 列表项目

GRUB_DEFAULT=saved
```
注释：该项将使用grub-set-default和grub-reboot命令来配置默认启动项
例如：
```bash
$ sudo grub-set-default 0
```
上面这条语句将会持续有效，直到下一次修改；下面的命令则只有下一次启动的时候生效：
```bash
$ sudo grub-reboot 0
```
将下次选择的启动项设为默认
只需要在`/etc/default/grub`中添加这行
```bash
GRUB_SAVEDEFAULT=true
```
隐藏引导菜单
```
GRUB_HIDDEN_TIMEOUT=X
```
注释：启动过程中，黑屏X秒后引导默认的启动项

 1. 注意1：启动过程中按SHIFT可以显示菜单
 2. 注意2：如果等号后面不跟任何值，也将会显示引导菜单

取消倒计时
```bash
GRUB_TIMEOUT=-1
```