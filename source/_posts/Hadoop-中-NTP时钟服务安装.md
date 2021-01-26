---
title: "Hadoop 中 NTP时钟服务安装"
categories: [ "Java" ]
tags: [ "hadoop","ntp" ]
draft: false
slug: "ntp-clock-service-installation"
date: "2015-12-01 13:48:00"
---

本文拟定是在一个局域网内（比如一个Hadoop集群）设定一台NTP服务器作为整个网络的标准时间参考，使用网络（集群）内的所有机器保持时间一致！以下是详细的操作步骤：


## 1. 修改选定的服务器的本地时间

```shell

    #date -s '2014-11-21 12:48:30' +'%F %T' #2014-11-21 12:48:30为将要设定的时间
```


<!--more-->


## 2. 将修改后的时间写入硬件时钟，确保重启有效
```
    #hwclock -w
```
## 3. 安装并开启ntp服务

在Centos7下，使用`systemctl is-enabled`ntpd查看ntpd是否已经配置为开机启动，如果没有则使用`systemctl enable ntpd`设置并重启。

## 4. 配置NTP服务器

在选定的NTP服务器（假定IP为10.221.18.114）上vim /etc/ntp.conf,

### 4.1. 将10.221.18.114的本地时钟作为时间供给源，这样，即便它失去网络连接，它也可以继续为网络提供服务;
```conf
    server 127.127.1.0
    fudge 127.127.1.0 stratum 10
```
如果集群是在一个封闭的局域网内，可以屏蔽掉默认的server:
```conf
    #server 0.centos.pool.ntp.org iburst
    #server 1.centos.pool.ntp.org iburst
    #server 2.centos.pool.ntp.org iburst
    #server 3.centos.pool.ntp.org iburst
```
### 4.2. 配置客户端的授权，也就是给指定的机器（客户端）设置访问NTP Server的权限，这是通过restrict配置项实现的，以下是它的格式说明。


[restrict]参数设定方式：
restrict [address] mask [netmask_ip] [parameter]


其中parameter的参数主要有：
ignore        ：    拒绝所有类型的ntp连接
nomodify    ：    客户端不能使用ntpc与ntpq两支程式来修改服务器的时间参数
noquery        ：    客户端不能使用ntpq、ntpc等指令来查询服务器时间，等于不提供ntp的网络校时
notrap        ：    不提供trap这个远程时间登录的功能
notrust        ：    拒绝没有认证的客户端
nopeer        ：    不与其他同一层的ntp服务器进行时间同步

让我们通过一个例子来解释一下，在 `/etc/ntp.conf`中加入如下一行：

`restrict 10.221.18.112 mask 255.255.255.240 nomodify notrap`

这一行的含义是授权`10.221.18.112`网段上的所有机器可以从这台机器上查询和同步时间。这里的配置涉及到了一些网络知识。 对于第一个参数[address] 它可能是一个IP，也可能是一个网段，这取决于后面给出的子网掩码。如果这里的子网掩码是`255.255.255.255`，那么配置就变成了只授权给IP是`10.221.18.112`的那一台机器连接！但是这里子网掩码是`255.255.255.240`，则此时的`10.221.18.112`就是一个网络标识了！它代表的是这样一个网段：
网络标识
（网段名）
	主机	广播地址
起始	结束
10.221.18.112	10.221.18.113	10.221.18.126	10.221.18.127

5. 配置NTP客户端

在所有客户端上vim /etc/ntp.conf，添加：

server 10.221.18.114

注意：当server与client之间的时间误差过大时（可能是1000秒），处于对修改时间可能对系统和应用带来不可预知的问题，NTP将停止时间同步！所以如果发现NTP启动之后时间并不进行同步时，应该考虑到可能是时间差过大引起的，此时需要先手动进行时间同步！

原文:[http://blog.csdn.net/bluishglc/article/details/41413031](http://blog.csdn.net/bluishglc/article/details/41413031)