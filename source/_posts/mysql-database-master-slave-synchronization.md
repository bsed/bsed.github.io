---
title: MySQL数据库主从同步
date: 2017-02-01 19:42:00
updated: 2017-02-12 13:26:58
tags: 
- javascript
- loader
- modules
categories: 
- js

---
## 环境准备：

安装两个MySQL数据库
【可以在两个不同的机器上、可以弄个虚拟机】

【在同一局域网内，分配好IP地址】

 - 主数据库：Master（192.168.1.101）
 - 从数据库：Slave（192.168.1.103）

## 具体步骤：

*前提：*
【主库】和【从库】要同步的表，表结构必须一致。主从数据库初始信息要相同，包括表结构、表数据、编码等。


<!--more-->


### 主库的设置：

配置my.ini
```mysql
[mysqld]
server-id=1     #配一个唯一的ID编号，1到232–1之间的一个正整数值
#server-id    #变量表示该节点在集群内的唯一表示，每个节点必须使用不同的server-id来区分 
log-bin=mysql-bin  #二进制文件存放路径   
```
## 设置要进行或不要进行主从复制的数据库名，同时也要在Slave 上设定
```
binlog-do-db = public_clouds  
binlog-do-db = 数据库名1
binlog-do-db = 数据库名2
binlog-ignore-db = 数据库名1
binlog-ignore-db = 数据库名2
```
## 在主数据库中建立一个备份账户
```mysql
mysql> grant replication slave,reload,super on *.* to backup@192.168.1.103 identified by 'hao';
mysql> flush privileges;         #flush privileges刷新MySQL的系统权限相关表
mysql> show master status;       # 找到File 和 Position 的值记录下来
```
保存并重启mysql服务
```bash
service mysqld restart
```
## 从库设置：

配置my.ini
```
[mysqld]
server-id=2     #唯一
# 设置要进行或不要进行主从复制的数据库名，同时也要在Master 上设定。
replicate-do-db = 数据库名1
replicate-do-db = 数据库名2
replicate-ignore-db = 数据库名1
replicate-ignore-db = 数据库名2

为slave开放权限
为`slave`账户开放全局许可权：Reload、Super、Replication Slave、Replication Client
```mysql
mysql> change master to master_host='192.168.1.101',master_user='backup',master_password='hao'; 
mysql> start slave;
mysql> show slave status\G;
```
如果出现，表示配置成功！
Slave_IO_Running: Yes
Slave_SQL_Running: Yes

错误信息的处理：
新加用户后，没有刷新用户权限，在 `master`上执行
```bash
flush privileges
```
当Slave_IO_Running: NO 或者 Slave_SQL_Running: NO
可以尝试，依次执行，
```
Stop slave;
reset slave;
show slave status\G;
```
## 基本操作命令：

 - reset slave; 重新启动slave
 - load data from master; 从主服务器载入数据。本命令用于对主服务器进行快照
 - File 列显示日志名，而 Position 显示偏移量
 - SHOW VARIABLES LIKE 'auto_in%';
 - SET auto_increment_increment=10; 设置增长幅度
 - SET auto_increment_offset=1; 设置初始值
 - relay-log 和 relay-log-index; 表示中继日志文件和中继日志索引文件