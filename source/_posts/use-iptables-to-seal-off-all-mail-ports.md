---
title: 使用 iptables 封掉所有邮件端口
date: 2016-11-14 21:08:00
updated: 2016-11-21 21:09:20
tags: 
- lnmp
- php
- nginx
categories: 
- linux

---
直接干掉所有邮件协议端口，解除烦脑。
```bash
iptables -A INPUT -p tcp -m multiport --dport 25,110,465:587,993:995 -j DROP
iptables -A INPUT -p udp -m multiport --dport 25,110,465:587,993:995 -j DROP
iptables -A OUTPUT -p tcp -m multiport --dport 25,110,465:587,993:995 -j DROP
iptables -A OUTPUT -p udp -m multiport --dport 25,110,465:587,993:995 -j 
```
保存iptables设置。
```bash
iptables-save
service iptables save
```