---
title: debian 禁用 ipv6 [运维篇]
date: 2018-11-21 18:02:00
updated: 2018-11-23 10:05:01
tags: 
- linux
- iptables
categories: 
- java

---
在我国ipv6是不可用的,那如何禁用提高解析速度。

找到文件：`/etc/sysctl.conf`
尾部添加：
```bash
# IPv6 disabled
net.ipv6.conf.all.disable_ipv6 =1
net.ipv6.conf.default.disable_ipv6 =1
net.ipv6.conf.lo.disable_ipv6 =1
```
重启：`sysctl -p`