---
title: "使用 iptables 封掉所有邮件端口 [运维篇]"
categories: [ "Linux" ]
tags: [ "linux","iptables" ]
draft: false
slug: "block-all-mail-ports-using-iptables-operations-and-maintenance-chapters"
date: "2018-11-22 18:10:00"
---

直接干掉所有邮件协议端口，解除烦脑。
```bash
iptables -A INPUT -p tcp -m multiport --dport 25,110,465:587,993:995 -j DROP
iptables -A INPUT -p udp -m multiport --dport 25,110,465:587,993:995 -j DROP
iptables -A OUTPUT -p tcp -m multiport --dport 25,110,465:587,993:995 -j DROP
iptables -A OUTPUT -p udp -m multiport --dport 25,110,465:587,993:995 -j 
```

<!--more-->


保存iptables设置。
```bash
iptables-save
service iptables save
```
