---
title: chentos上 nfs安装
date: 2015-06-30 21:24:00
updated: 2015-09-04 21:26:10
tags: 
- golang
- upload
categories: 
- go

---
##服务器信息
120.25.157.15   内网ip: 10.116.141.193  
120.25.229.39   内网ip: 10.116.100.11

## NFS多台centos服务器搭建
现在有三台服务器
s1(主)，s2(从), s3（从）

安装 NFS 服务器所需的软件包：
# yum install nfs-utils

## 编辑exports文件
# vim /etc/exports
/data 192.168.0.100(rw,sync,fsid=0)  192.168.0.101(rw,sync,fsid=0)

192.168.0.100 和192.168.0.101两台机器可以挂载NFS服务器上的/data目录到自己的文件系统中


<!--more-->


rw表示可读写；sync表示同步写，fsid=0表示将/data找个目录包装成根目录

启动nfs服務：
先为rpcbind和nfs做水即启动：
#chkconfig rpcbind on
#chkconfig nfs on

然后分别启动rpcbind和nfs服务：
#service rpcbind start
#service nfs start

确认NFS服务器启动成功：
# rpcinfo -p

检查 NFS 服务器是否挂载我们想共享的目录 /data：
# exportfs
/data         	192.168.0.100
/data         	192.168.0.101

NFS 客户端
首先是安裝nfs。
然后启动rpcbind服务：

先为rpcbind做随机启动：
> chkconfig rpcbind on
然后启动rpcbind服务：
> service rpcbind start

注意：客户端不需要启动nfs服务

检查 NFS 服务器端是否有目录共享：
> showmount -e nfs服务器的IP

在从机上使用 mount 挂载服务器端的目录/data到客户端某个目录下：
> mkdir /data
> mount -t nfs4 nfs服务器IP:/    /data
> df -h 查看是否挂载成功

想在客户机上实现开机挂载，则在/etc/fstab加上：
> vi /etc/fstab
> nfs服务器IP:/   /data  nfs4 ro,hard,intr,proto=tcp,port=2049,noauto 0 0

客户端挂载后，写文件时可能会报一个权限问题：Permission denied。解决这个问题可以修改/etc/exports，改成如下內容：
> vi /etc/exports
> /data  192.168.0.100(rw,sync,fsid=0,anonuid=xx,anongid=xx) 192.168.0.101(rw,sync,fsid=0,anonuid=xx,anongid=xx)

查看服务器uid
> id nfsnobody 
> uid=65534(nfsnobody) gid=65534(nfsnobody) groups=65534(nfsnobody)

讲xx改成65534
 
重新读取NFS配置文件：
> exportfs -rv

可使用如下命令查看
> exportfs -v

参考：http://www.server110.com/linux/201404/10169.html