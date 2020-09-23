---
title: Debian / Ubuntu 服务器下安装 net-speeder 教程
date: 2015-05-12 12:46:00
updated: 2015-05-12 18:53:58
tags: 
- vim
- Tabular
categories: 
- java

---
官方介绍： [net-speeder](https://github.com/snooda/net-speeder) 可以通过 TCP 多倍发包的方式在高延迟不稳定链路上优化单线程下载速度。

+换言之， net-speeder 可以通过多倍发包（消耗多倍流量），减小丢包率，从而保证带宽。因为中美之间线路总是隔三差五出现各种问题，虽然国家还不富裕，但是流量相对于现在的使用还是相当富足的，用流量换带宽不失为一个在小流量范围内（网站小于 1000k ip/day XD）的好办法。


<!--more-->


+当然不仅仅能用于加速，启用后对科学上网的速度也能够保证。

+//AD: [DigitalOcean](https://www.digitalocean.com/?refcode=f547bf00e76e) 512MB Ram
20GB SSD 1000G Traffic VPS 只需 $5/mo ，注册即赠送 $10 ，五地机房可选

+经过我在网站服务器 DigitalOcean VPS 上的测试，下载速度从 60kb/s 提升到了 500kb/s （本地使用的是 6M 宽带，几乎跑满），效果还是比较显著的。

+官方对于安装的命令说明过于精简，可能对我这样的小白比较困难，下面分享一下我的安装经验。

## 更新 ##

    $ apt-get update
    $ apt-get upgrade

## 安装 net-speeder 依赖的库 ##

    apt-get install libnet1
    apt-get install libpcap0.8
    apt-get install libnet1-dev
    apt-get install libpcap0.8-dev

## 下载源码 ##

    $ cd /home
    $ wget https://github.com/snooda/net-speeder/raw/master/net_speeder.c
    $ wget https://github.com/snooda/net-speeder/raw/master/build.sh

## 更改权限 ##

`$chmod +x build.sh;`

##　编译　##

`$./build.sh -DCOOKED`

## 使用 nohup 使其持续运行并将其加入开机启动 ##

    $nohup /home/net_speeder eth0 "ip";echo "nohup /home/net_speeder eth0 "ip" >/dev/null 2>&1 &" >> /etc/rc.local

link:[http://www.dynamicer.com/2015/02/25/install-and-configure-shadowsocks-on-debian-or-ubuntu-servers/](http://www.dynamicer.com/2015/02/25/install-and-configure-shadowsocks-on-debian-or-ubuntu-servers/)

