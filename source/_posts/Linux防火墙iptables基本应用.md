---
title: "Linux防火墙iptables基本应用"
categories: [ "Linux" ]
tags: [ "linux","iptables" ]
draft: false
slug: "the-basic-application-of-linux-firewall-iptables"
date: "2016-11-05 21:11:00"
---

iptables是一个linux的基础组件，主要应用于防火墙应用，例如禁止某IP访问，或者禁止一些端口，提升网站的安全性。一般情况下，系统默认就会安装，如果没有安装，可自行安装。
## 语法
iptables(选项)(参数)
选项

 - -t<表>：指定要操纵的表；
 - -A：向规则链中添加条目；
 - -D：从规则链中删除条目；
 - -i：向规则链中插入条目；
 - -R：替换规则链中的条目；
 - -L：显示规则链中已有的条目；
 - -F：清楚规则链中已有的条目；
 - -Z：清空规则链中的数据包计算器和字节计数器；
 - -N：创建新的用户自定义规则链；
 - -P：定义规则链中的默认目标；
 - -h：显示帮助信息；
 - -p：指定要匹配的数据包协议类型；
 - -s：指定要匹配的数据包源ip地址；
 - -j<目标>：指定要跳转的目标；
 - -i<网络接口>：指定数据包进入本机的网络接口；
 - -o<网络接口>：指定数据包要离开本机所使用的网络接口。

iptables命令选项输入顺序：

 - iptables -t 表名 <-A/I/D/R> 规则链名 [规则号] <-i/o 网卡名> -p 协议名 <-s 源IP/源子网>
   --sport 源端口 <-d 目标IP/目标子网> --dport 目标端口 -j 动作

表名包括：

 - raw：高级功能，如：网址过滤。
 - mangle：数据包修改（QOS），用于实现服务质量。
 - net：地址转换，用于网关路由器。
 - filter：包过滤，用于防火墙规则。

规则链名包括：

 - INPUT链：处理输入数据包。
 - OUTPUT链：处理输出数据包。
 - FORWARD链：处理转发数据包。
 - PREROUTING链：用于目标地址转换（DNAT）。
 - POSTOUTING链：用于源地址转换（SNAT）。

动作包括：

 - accept：接收数据包。
 - DROP：丢弃数据包。
 - REDIRECT：重定向、映射、透明代理。
 - SNAT：源地址转换。
 - DNAT：目标地址转换。
 - MASQUERADE：IP伪装（NAT），用于ADSL。
 - LOG：日志记录。

## 实例

```bash
yum install iptables #centos系统
apt-get install iptables #debian/ubuntu系统
```
### 先说一下几个常用命令：
```bash
service iptables -status #查看iptables是否运行
iptables -F #大写F，清空当前所有规则
iptables-save #注意s-之间无空格，保存当前规则
iptables -L -n #列表显示当前规则
```
### 常见封ip命令：
```bash
iptables -A INPUT -s 123.45.67.89 -j DROP #封掉单个IP地址
#屏蔽整个段（A类地址）即从192.0.0.1到192.255.255.254，则添加以下规则。
iptables -I INPUT -s 192.0.0.0/8 -j DROP
#封IP段（B类地址）即从192.168.0.1到192.168.255.254，则添加以下规则。
iptables -I INPUT -s 192.168.0.0/16 -j DROP
#封IP段（C类地址）即从192.168.200.1到192.168.200.254，则添加以下规则。
iptables -I INPUT -s 192.168.200.0/24 -j DROP
```
### 查看及删除命令：
```bash
iptables -L -n --line-numbers #按行列表显示当前规则
iptables -D INPUT 15 #删除第15条规则
```
### 清除已有iptables规则
```bash
iptables -F
iptables -X
iptables -Z
```
## 开放指定的端口
```bash
iptables -A INPUT -s 127.0.0.1 -d 127.0.0.1 -j ACCEPT               #允许本地回环接口(即运行本机访问本机)
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT    #允许已建立的或相关连的通行
iptables -A OUTPUT -j ACCEPT         #允许所有本机向外的访问
iptables -A INPUT -p tcp --dport 22 -j ACCEPT    #允许访问22端口
iptables -A INPUT -p tcp --dport 80 -j ACCEPT    #允许访问80端口
iptables -A INPUT -p tcp --dport 21 -j ACCEPT    #允许ftp服务的21端口
iptables -A INPUT -p tcp --dport 20 -j ACCEPT    #允许FTP服务的20端口
iptables -A INPUT -j reject       #禁止其他未允许的规则访问
iptables -A FORWARD -j REJECT     #禁止其他未允许的规则访问
```
### 屏蔽IP
```bash
iptables -I INPUT -s 123.45.6.7 -j DROP       #屏蔽单个IP的命令
iptables -I INPUT -s 123.0.0.0/8 -j DROP      #封整个段即从123.0.0.1到123.255.255.254的命令
iptables -I INPUT -s 124.45.0.0/16 -j DROP    #封IP段即从123.45.0.1到123.45.255.254的命令
iptables -I INPUT -s 123.45.6.0/24 -j DROP    #封IP段即从123.45.6.1到123.45.6.254的命令是
```
### 查看已添加的iptables规则
```bash
iptables -L -n -v
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
 247M   38G ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED 
  114  6744 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0           
 134K 8062K ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0           
  592 31312 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:22 
   11   648 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:80 
 106K 5686K ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:3306 
20895 1260K ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:9090 
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:9092 
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:9099 
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:9095 
   36  1856 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:6379 
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:13001 
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:11000 
 1530 97680 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:8189 
 342K   32M REJECT     all  --  *      *       0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited 
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:13001 
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:11000 

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 REJECT     all  --  *      *       0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited 

Chain OUTPUT (policy ACCEPT 260M packets, 65G bytes)
 pkts bytes target     prot opt in     out     source               destination 
```
### 删除已添加的iptables规则

将所有iptables以序号标记显示，执行：
```bash
iptables -L -n --line-numbers
```
比如要删除INPUT里序号为8的规则，执行：
```bash
iptables -D INPUT 8
```