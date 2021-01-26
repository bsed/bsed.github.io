---
title: "iptables之禁ping和ddos向外发包 [运维篇]"
categories: [ "Java" ]
tags: [ "linux","iptables" ]
draft: false
slug: "iptables-ban-ping-and-ddos-outsourcing-operations-and-maintenance-chapter"
date: "2018-11-22 18:08:00"
---

主要讲2个基本的实际应用，主要涉及到禁ping（ipv4）以及禁止udp，即禁止有黑客利用服务器向外发包ddos攻击方面的内容。

## 一、如果没有iptables禁止ping
```bash
echo 1 > /proc/sys/net/ipv4/icmp_echo_igore_all #开启

echo 0 > /proc/sys/net/ipv4/icmp_echo_igore_all #关闭
```


<!--more-->


## 二、利用iptables规则禁ping
```bash
iptables -A INPUT -p icmp --icmp-type 8 -s 0/0 -j DROP
```
## 三、利用iptables规则，禁止服务器向外发包，防止DDOS向外攻击
```bash
iptables -I OUTPUT -p udp --dport 53 -d 8.8.8.8 -j ACCEPT #允许UDP服务IP

iptables -A OUTPUT -p udp -j DROP #禁止udp服务
```
上述53端口和8888是DNS服务必须有的，如果不清楚本机的DNS设置，可执行以下命令得到IP：
```bash
cat /etc/resolv.conf
```
