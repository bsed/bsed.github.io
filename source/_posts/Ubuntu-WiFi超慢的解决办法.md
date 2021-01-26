---
title: "Ubuntu WiFi超慢的解决办法"
categories: [ "Linux" ]
tags: [ "linux","wifi" ]
draft: false
slug: "ubuntu-wifi-ultra-slow-solution"
date: "2014-02-13 21:46:00"
---

现象是，同样的路由器，Windows连接后网络访问很快，Ubuntu的笔记本网络访问很慢，路由器显示其经常掉线，ping路由器的延时很大，多数达到几百上千。

据说是由于Ubuntu的配置引起的，有多种措施。

 

方法一：勾选IPv4选项（经测试，可以稍稍提高一点WiFi速度）

“编辑连接”->“IPv4设置”->勾选“需要IPv4地址完成这个连接”。

## 方法二：配置ath9k（经测试，可以提高ubuntu WiFi稳定性和速度，降低对路由器的ping值）

编辑或新建文件`/etc/modprobe.d/ath9k.conf`
在末尾加入内容`options ath9k nohwcrypt=1`
保存后退出重启WiFi


<!--more-->


也可以用如下命令实现相同措施
```bash
sudo ifconfig wlan0 down
sudo rmmod -f ath9k
sudo modprobe ath9k nohwcrypt=1
sudo ifconfig wlan0 up
```

## 方法三：强制电脑关闭11n功能（未测试）
在终端中输入如下命令：
```bash
sudo rmmod -f iwlagn
sudo modprobe iwlagn 11n_disable=1
```
如果上面的命令生效，打开问下文件：
```bash
gksudo gedit /etc/modprobe.d/iwlagn-disable11n.conf
```
写入`options iwlagn 11n_disable=1`

然后保存重启。这样就永久生效了。

## 方法四：禁用IPv6功能（未测试）
在终端中输入如下命令，然后重启电脑
```
echo "#disable ipv6" | sudo tee -a /etc/sysctl.conf
echo "net.ipv6.conf.all.disable_ipv6 = 1" | sudo tee -a /etc/sysctl.conf
echo "net.ipv6.conf.default.disable_ipv6 = 1" | sudo tee -a /etc/sysctl.conf
echo "net.ipv6.conf.lo.disable_ipv6 = 1" | sudo tee -a /etc/sysctl.conf
```

