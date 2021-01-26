---
title: "Linux防火墙iptables基本应用 [运维篇]"
categories: [ "Java" ]
tags: [ "linux","iptables" ]
draft: false
slug: "basic-applications-of-linux-firewall-iptables-operations-and-maintenance"
date: "2018-11-21 19:05:00"
---

> iptables是一个linux的基础组件，主要应用于防火墙应用，例如禁止某IP访问，或者禁止一些端口，提升网站的安全性。一般情况下，系统默认就会安装，如果没有安装，可自行安装。
```bash
yum install iptables #centos系统
apt-get install iptables #debian/ubuntu系统
```
## 一、先说一下几个常用命令：


<!--more-->


```bash
service iptables -status #查看iptables是否运行

iptables -F #大写F，清空当前所有规则

iptables-save #注意s-之间无空格，保存当前规则

iptables -L -n #列表显示当前规则
```
## 二、常见封ip命令：

```bash
iptables -A INPUT -s 123.45.67.89 -j DROP #封掉单个IP地址
#屏蔽整个段（A类地址）即从192.0.0.1到192.255.255.254，则添加以下规则。
iptables -I INPUT -s 192.0.0.0/8 -j DROP

#封IP段（B类地址）即从192.168.0.1到192.168.255.254，则添加以下规则。
iptables -I INPUT -s 192.168.0.0/16 -j DROP

#封IP段（C类地址）即从192.168.200.1到192.168.200.254，则添加以下规则。
iptables -I INPUT -s 192.168.200.0/24 -j DROP
```

## 三、查看及删除命令：
```bash
iptables -L -n --line-numbers #按行列表显示当前规则

iptables -D INPUT 15 #删除第15条规则
```
