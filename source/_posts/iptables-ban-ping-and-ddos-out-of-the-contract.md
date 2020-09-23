---
title: iptables之禁ping和ddos向外发包
date: 2016-11-17 05:09:00
updated: 2018-08-23 17:03:56
tags: 
- linux
- crontab
categories: 
- linux

---
主要讲2个基本的实际应用，主要涉及到禁ping（ipv4）以及禁止udp，即禁止有黑客利用服务器向外发包ddos攻击方面的内容。

## 一、如果没有iptables禁止ping
```bash
echo 1 > /proc/sys/net/ipv4/icmp_echo_igore_all #开启
echo 0 > /proc/sys/net/ipv4/icmp_echo_igore_all #关闭
```
## 二、利用iptables规则禁ping


<!--more-->


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


## CentOS 7 以上版本默认使用的是firewall作为防火墙，改为iptables防火墙步骤。

### 1、关闭firewall：
```bash
systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动
firewall-cmd –state #查看默认防火墙状态（关闭后显示notrunning，开启后显示running）
```
### 2、安装iptables防火墙
```bash
yum install iptables-services
```
### 3、启动iptables防火墙
```bash
vi/etc/sysconfig/iptables #编辑防火墙配置文件，添加22、80和3306端口
-A INPUT -p tcp -mstate –state NEW -m tcp –dport 22 -j ACCEPT
-A INPUT -p tcp -m state –state NEW -m tcp –dport 80 -jACCEPT
-A INPUT -p tcp -m state –state NEW -m tcp –dport 3306-j ACCEPT
:wq! #保存退出

#最后重启防火墙使配置生效
systemctl restart iptables.service
#设置防火墙开机启动
systemctl enable iptables.service
```
### 4、关闭SELINUX
```
vi /etc/selinux/config
#注释以下配置
SELINUX=enforcing
SELINUXTYPE=targeted

#增加以下配置
SELINUX=disabled

#使配置立即生效
setenforce 0
```
iptables 详细配置可以访问我以前写的文章 [Linux 防火墙 iptables 基本应用](https://tygasoft.com/the-basic-application-of-linux-firewall-iptables.html)