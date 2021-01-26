---
title: "一些ip 命令"
categories: [ "Linux" ]
tags: [ "linux","bash" ]
draft: false
slug: "some-ip-commands"
date: "2018-03-17 11:44:00"
---

```bash
$ ip addr show # 查看网卡信息
# 或
$ ip a s
$ ip addr show eth0 # 查看 eth0 网卡
$ ip link set eth0 up # 启用网卡
$ ip link set eth0 down # 禁用网卡
$ ip addr add 192.168.0.50/255.255.255.0 dev eth0 # 为网卡分配ip地址


<!--more-->


$ ip addr add broadcast 192.168.0.255 dev eth0 # 为网卡分配广播地址
$ ip addr add 192.168.0.10/24 brd + dev eth0 # 根据 IP 地址设置标准的广播地址,可以使用 brd 代替 broadcast 来设置广播地址
$ ip addr del 192.168.0.10/24 dev eth0 # 删除网卡中配置的 IP 地址
$ ip addr add 192.168.0.20/24 dev enp0s3 label eth0:1 # 为网卡添加多个IP
$ ip route show # 查看网络路由信息
$ ip route get 192.168.0.1 # 获取特定IP的路由信息
$ ip route add default via 192.168.0.150/24 # 添加静态路由
$ ip route add 172.16.32.32 via 192.168.0.150/24 dev eth0 # 修改某个网卡的默认路由
$ ip route del 192.168.0.150/24 # 删除默认路由
# 用上面方法修改的默认路由只是临时有效的，在系统重启后所有的改动都会丢失。要永久修改路由，需要修改或创建 route-enp0s3 文件
$ sudo vi /etc/sysconfig/network-scripts/route-enp0s3
172.16.32.32 via 192.168.0.150/24 dev enp0s3
# 基于 Ubuntu 或 debian 的操作系统，则该要修改的文件为 /etc/network/interfaces，然后添加 ip route add 172.16.32.32 via 192.168.0.150/24 dev enp0s3 这行到文件末尾
$ ip neigh # 查看 ARP 缓存中的记录
ip neigh del 192.168.0.106 dev eth0 # 删除 ARP 记录
$ ip neigh add 192.168.0.150 lladdr 33:1g:75:37:r3:84 dev enp0s3 nud perm # 往 ARP 缓存中添加新记录
# nud 的意思是 “neghbour state”（网络邻居状态），它的值可以是
# perm - 永久有效并且只能被管理员删除
# noarp - 记录有效，但在生命周期过期后就允许被删除了
# stale - 记录有效，但可能已经过期
# reachable - 记录有效，但超时后就失效了
$ ip -s link # 查看网络的统计信息，比如所有网卡上传输的字节数和报文数，错误或丢弃的报文数
$ ip help
```