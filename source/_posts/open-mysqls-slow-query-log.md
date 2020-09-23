---
title:  打开MySQL的慢查询记录
date: 2009-02-23 08:05:00
updated: 2015-11-01 08:07:08
tags: 
- java
- api
categories: 
- java

---
## 打开MySQL慢查询

MySQL慢查询记录日志对于跟踪PHP+MySQL体系下的MySQL负载调优问题很有用处，比如安装了很多Discuz!插件的用户，这样可以大概排查出那些插件有代码问题。其实启用MySQL的慢查询日志很简单,只需要在MySQL的配置文件里添加log-slow-queries和long_query_time两个参数即可。

今天有个朋友问我，就顺带记录上来。更多的MySQL优化信息可以查看这里：http://www.ccvita.com/category/mysql

## Windows下开启MySQL慢查询


<!--more-->


MySQL在Windows系统中的配置文件一般是是my.ini找到[mysqld]下面加上

    log-slow-queries = F:\MySQL\log\mysqlslowquery.log
    long_query_time = 2

## Linux下启用MySQL慢查询

MySQL在Windows系统中的配置文件一般是是my.cnf找到[mysqld]下面加上

    log-slow-queries=/data/mysqldata/slowquery.log
    long_query_time=2

*注意:*

`log-slow-queries = F:\MySQL\log\mysqlslowquery.log`为慢查询日志存放的位置，一般这个目录要有MySQL的运行帐号的可写权限，一般都将这个目录设置为MySQL的数据存放目录；
long_query_time=2中的2表示查询超过两秒才记录；

在`my.cnf`或者 `my.ini` 中添加 `log-queries-not-using-indexes` 参数，表示记录下没有使用索引的查询。比如：

    log-slow-queries=/data/mysqldata/slowquery.log
    long_query_time=2
    log-queries-not-using-indexes