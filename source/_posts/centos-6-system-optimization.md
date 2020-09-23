---
title: centos 6 系统优化
date: 2013-11-10 07:52:00
updated: 2015-11-07 07:56:55
tags: 
- linux
- lshw
categories: 
- linux

---
公司开发环境使用的是centos+virtualbox,只是为了方便开发做了一些基本的优化,既然是内部开发环境,首先保证的是开发方便少灵异事件,其它安全性和性能基本没有要求.

## 1.关闭iptables 和selinux
iptables:

    service iptables stop
    chkconfig iptables off
    service ip6tables stop
    chkconfig ip6tables off

selinux:
临时关闭 `setenforce 0`
永久关闭 `vi /etc/selinux/config` 加入或更改为` SELINUX=disabled` 注意需要重启生效

## 2.调整最大打开文件数调整为65535

系统默认为1024

`vi /etc/security/limits.conf`


<!--more-->


加入

    *           soft   nofile       65535
    *           hard   nofile       65535

## 3.关闭ipv6

    vi /etc/modprobe.d/dist.conf

在最后加入

    alias net-pf-10 off
    alias ipv6 off    之后重启电脑

## 4.每天四点和国家授时中心进行时间校正

    vi /etc/crontab

加入

    * 4 * * * /usr/sbin/ntpdate 210.72.145.44 

## 5.vi 默认开启 语法高亮 显示行号 鼠标定位等

    vi /etc/vimrc

加入`syntax on`和` set nu`
如果想用在vi中使用鼠标确定位置，可以加入set mouse=a（我没加，视个人爱好）

## 6.增加admin组用户默认有sudo 权限

    visudo

加入

    %admin ALL=(ALL)       NOPASSWD: ALL

保存退出，之后：

    groupadd admin

这样以后加有管理员权限的账户只要用 useradd -Gadmin XXX 就可以了

## 7.安装 Vbox Guset Additions

点虚拟机窗口上的设备－－>安装增强功能,之后登陆终端
a.挂光驱

    mkdir /cdrom
    mount /dev/sr0 /cdrom  (是不是sr0 视个人情况)

b. 安装依赖环境

    yum install gcc
    yum instal kernel-devel
    yum install dkms

c.安装

    cd /cdrom
    ./VBoxLinuxAdditions.run

d.重启测试 （鼠标自动移出 等）

## 8.禁用GSSAPI和 DNS反向解析来提高ssh连接速度

同时关闭空密码用户登陆，允许root login

    vi /etc/ssh/sshd_config

设置以下几项为

    GSSAPIAuthentication no
    UseDNS no
    PermitEmptyPasswords no
    PermitRootLogin yes

保存service sshd restart
## 9.设置用户6小时未操作注销终端

    vi /etc/profile
    export TMOUT=21600

## 10.取消生成whatis数据库和locate数据库，防止重建数据库时cpu内存占用过高的情况

    rm -rf /etc/cron.daily/makewhatis.cron 
    rm -rf /etc/cron.daily/mlocate.cron 

## 11.tcp 性能优化

vi  /etc/sysctl.conf

加入

    net.ipv4.tcp_fin_timeout = 1 
    net.ipv4.tcp_keepalive_time = 1200
    net.ipv4.tcp_mem = 94500000 915000000 927000000 
    net.ipv4.tcp_tw_reuse = 1 
    net.ipv4.tcp_timestamps = 0 
    net.ipv4.tcp_synack_retries = 1 
    net.ipv4.tcp_syn_retries = 1 
    net.ipv4.tcp_tw_recycle = 1 
    net.core.rmem_max = 16777216 
    net.core.wmem_max = 16777216 
    net.core.netdev_max_backlog = 262144 
    net.core.somaxconn = 262144 
    net.ipv4.tcp_max_orphans = 3276800 
    net.ipv4.tcp_max_syn_backlog = 262144 
    net.core.wmem_default = 8388608 
    net.core.rmem_default = 8388608 

## 12.关闭并取消没用程序的开机启动查看所有服务

    chkconfig --list

查看启动的服务 `chkconfig --list |grep :on`
先用`service` 服务名 `stop` 关闭
在用`chkconfig` 服务名 `off` 取消开机启动
只保留以下程序

    NetworkManager 
    cpuspeed 
    crond
    haldaemon
    irqbalance
    kdump
    lvm2-monitor
    messagebus 
    portreserve 
    rsyslog 
    spice-vdagentd 
    sshd 
    sysstat
    udev-post 
    vboxadd 
    vboxadd-service 
    vboxadd-x11

13.日志定时清除

    # backup sys log every day 1:01 
    28 04 * * * root /opt/scripts/cron/backuplog.sh 
    # clear backup sys log every month 1 1:30 
    30 1 1 * * root /opt/scripts/cron/clearlog.sh
    /opt/scripts/cron/backuplog.sh 
    #!/bin/sh 
    Day=$(date +%Y%m%d) 
    tar -cf /var/log/wtmp_"$Day".tar.gz /var/log/wtmp
    echo "">/var/log/wtmp 
    tar -cf /var/log/utmp_"$Day".tar.gz /var/run/utmp
    echo "">/var/run/utmp
    /opt/scripts/cron/clearlog.sh 
    #!/bin/sh
    Day=$(date -d "-2 month" +%Y%m) 
    rm -f /var/log/wtmp_"$Day"*.tar.gz
    rm -f /var/log/utmp_"$Day"*.tar.gz 

## 14.安装jdk telnet mysql 等常用软件

mysql未启动，使用请chkconfig mysql on service mysql start 即可

## 15 禁用ctrl+alt+del自动重启

注释掉

    exec /sbin/shutdown -r now "Control-Alt-Delete pressed" 

## 16 删除日志，命令行记录 导出虚拟机

    rm -rf /var/log/wtmp
    rm -rf /var/run/utmp
    rm -rf /root/.bash_history

经测试vbox 配置双核 1G时 ,使用上面的镜像开机速度可以在10秒左右,当然linux的开机速度提升并没有实际意义
转载：[https://www.52os.net/articles/centos-6-system-optimize.html](https://www.52os.net/articles/centos-6-system-optimize.html)










