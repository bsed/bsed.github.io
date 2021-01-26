---
title: "CentOS设置系统时间与网络时间同步"
categories: [ "Linux" ]
tags: [ "linux","centos","ntp" ]
draft: false
slug: "centos-set-system-time-and-network-time-synchronization"
date: "2018-08-14 16:15:00"
---

Linux的时间分为System Clock（系统时间）和Real Time Clock （硬件时间，简称RTC）。

系统时间：指当前Linux Kernel中的时间。

硬件时间：主板上有电池供电的时间。

查看系统时间的命令： `date`

设置系统时间的命令： `date –set（月/日/年 时：分：秒）`

例：`date –set "10/11/10 10:15"`


<!--more-->


查看硬件时间的命令： `hwclock`

设置硬件时间的命令： `hwclock –set –date = （月/日/年 时：分：秒）`

上述提到的是手动设置时间到一个时间点，可能与当前网络的时间有误差。下面介绍一下与时间服务器上的时间同步的方法

1.  安装ntpdate工具
```bash
yum -y install ntp ntpdate
```
2.  设置系统时间与网络时间同步
```bash
ntpdate cn.pool.ntp.org
```
3.  将系统时间写入硬件时间

```bash
hwclock --systohc
```
4.强制系统时间写入CMOS中防止重启失效
```bash
hwclock -w
或clock -w
```
我们在安装完Centos Linux操作系统之后，点击系统的时间发现与现在所使用的时间不一致，相差有8小时，而在安装系统的时候我们选择的时区是上海，但是CentOS Linux默认的bios时间是utc时间(UTC是协调世界时(Universal Time Coordinated)英文缩写，是由国际无线电咨询委员会规定和推荐,并由国际时间局(BIH)负责保持的以秒为基础的时间标度。UTC相当于本初子午线(即经度0度)上的平均太阳时，过去曾用格林威治平均时(GMT)来表示.北京时间比UTC时间早8小时，以1999年1月1日0000UTC为例，UTC时间是零点，北京时间为1999年1月1日早上8点整。)，所以我们在时间上面相隔了8个小时。这个时候bios的时间和系统的时间当然是不一致，一个代表 utc 时间，一个代表cst（＋8时区），即上海的时间。

让我们动手将操作系统的时间进行同步吧!

在CentOS Linux中终端命令中执行以下命令：

1、`vi /etc/sysconfig/clock`   #编辑时间配置文件
```
ZONE="Asia/Shanghai"
UTC=false                          #设置为false，硬件时钟不于utc时间一致
ARC=false
```
2、`ln -sf /usr/share/zoneinfo/Asia/Shanghai    /etc/localtime`    #linux的时区设置为上海时区

3、ntpdate 192.43.244.18    #对准时间

如果没有安装ntp服务器，刚需要先执行以下命令：
```bash
yum install ntp #安装ntp服务器
```
4、/sbin/hwclock --systohc   #设置硬件时间和系统时间一致并校准

好了，我们的CentOS linux系统时间和计算机硬件时间终于都是cst时间了，并且都是上海时区，终于是正常啦。

原文：[https://blog.csdn.net/u011391839/article/details/62892020](https://blog.csdn.net/u011391839/article/details/62892020)