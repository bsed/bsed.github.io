---
title: Debian下一键安装PPTP VPN
date: 2015-12-24 21:44:00
updated: 2016-12-02 21:55:47
tags: 
- css
categories: 
- css

---
一个自动安装PPTP VPN的安装脚本，适用于Debian或Ubuntu系统，支持OpenVZ架构的VPS。Xen架构的VPS暂未测试，话说网上很多Xen架构上安装VPN的教程。
注意：这个安装脚本并不能运行在所有的OpenVZ架构的VPS上，大地在BandwagonHost这家的VPS上安装成功。推荐系统环境：Debian 5,6和Ubuntu 11.04。
【检查PPP模块】
root帐户，通过SSH登录到你的OpenVZ VPS，敲入：
```bash
cat /dev/ppp
```
如返回下面一行，则表示PPP模块是开启着的。
```bash
cat: /dev/ppp: No such device or address
```
如果不是，唔，联系你的主机商吧，一般都会给开启的；或在SolusVM管理面板中开启，或关闭再重新打开（按键具体叫什么名字忘了，反正是有的，汗～）。
【下载并运行脚本】
分别运行下面三行：
```bash
wget http://dadi.me/wp-content/uploads/dir/VPN/pptp_ovz_debian.sh
chmod +x pptp_ovz_debian.sh
./pptp_ovz_debian.sh
```
下面是一些安装选项简介：


<!--more-->


1) Set up new PoPToP server AND create one user
# 键入1，回车、则安装PPTP服务，并创建一个用户。
2) Create additional users
# 键入2，回车、则添加用户。
Enter username that you want to create (eg. vpn or dadi.me):
# 需要创建的VPN登录所需的用户名。
Specify password that you want the server to use:
# 对应VPN用户的密码。
最后输入密码之后就开始自动安装了，或是添加用户。
【额外】
如果有些网站不能访问，PPTP VPN安装之后，还需要在登录SSH后敲入并运行：
```bash
iptables -I FORWARD -p tcp --syn -i ppp+ -j TCPMSS --set-mss 1356
```
【结语】
运行之后，选择2，是添加用户；这一步骤适用于其他脚本安装的PPTP VPN，貌似L2TP也是可以的。
说到添加和管理用户，大地记得Webmin软件也是支持管理的，可以直接通过Web访问控制，添加或删除PPTP VPN用户。也简单，PPTP VPN安装之后，再正常步骤安装Webmin即可；有可能会在之后的文章中出现。

重启pptp服务。
```
/etc/init.d/pptpd restart
```
原文：http://blog.liangjinjin.cn/?p=348