---
title: OpenVZ 环境 Debian下安装PPTP VPN
date: 2014-10-05 16:13:00
updated: 2015-10-05 16:17:59
tags: 
- java
- maven
categories: 
- maven

---
> 昨天推荐了搬瓦工的vps搬瓦工bandwagonhost最低年付
> $3.99，用它搭建vpn速度相当快，看youtube一点也不卡，用来做vpn是再合适不过的选择。由于是小内存，我一般都安装debian系统。所以今天给大家介绍下如何安装pptp
> vpn。 检测是否开启`TUN`和`PPP`

## 1、联系你的服务器提供商，直接询问。如果没有开启，可以要求他们开启。

## 2、自行检测：
检测TUN

    cat /dev/net/tun

返回;


<!--more-->


    cat: /dev/net/tun: File descriptor in bad state

说明正常

检测是支持ppp模块

    cat /dev/ppp

返回:

    cat: /dev/ppp: No such device or address

说明正常
脚本使用说明

首先下载脚本，使用putty登陆vps，运行以下命令：


    wget --no-check-certificate https://raw.githubusercontent.com/tennfy/debian_pptp_tennfy/master/debian_pptp_tennfy.sh
    chmod a+x debian_pptp_tennfy.sh

本脚本提供了安装、卸载、修复619错误等三种命令，下面分别对这三个命令进行介绍
1、安装PPTP VPN

1）安装PPTP VPN，执行以下代码：

    ./debian_pptp_tennfy.sh install

2）添加PPTP VPN账号密码，执行以下代码：

    ./debian_pptp_tennfy.sh adduser

根据提示添加用户名以及密码。
## 2、修复619错误

如果出现619错误，那么执行

    ./debian_pptp_tennfy.sh repaire

## 3、卸载PPTP VPN

如果想要卸载PPTP VPN，那么执行

    ./debian_pptp_tennfy.sh uninstall

*更新历史:*

更新日期：2015-3-1，大幅度更新脚本，并托管在github上。

参考文章：[Buyvm OpenVZ debain6.0 VPS 搭建PPTP-VPN笔录](http://blog.yanwen.org/archives/1386.html)
