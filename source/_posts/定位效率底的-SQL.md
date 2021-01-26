---
title: "定位效率底的 SQL"
categories: [ "SQL" ]
tags: [ "mysql" ]
draft: false
slug: "positioning-efficiency-at-the-end-of-the-sql"
date: "2010-06-14 22:53:00"
---

## 一般通过以下两种方式定位执行效率较低的 SQL 语句。

通过慢查询日志定位那些执行效率较低的SQL语句，用 `—log-slow-queries[=file_name]` 选项启动时， mysqld 会 写一个包含所有执行时间超过 `long_query_time` 秒的 SQL 语句的日志文件，通过查看这个日志文件定位效率较低的 SQL 。
慢查询日志在查询结束以后才纪录，所以在应用反映执行效率出现问题的时候查询慢查询日志并不能定位问题，可以使用 `show processlist` 命令查看当前 MySQL 在进行的线程，包括线程的状态、是否锁表等，可以实时地查看 SQL 的 执行情况，同时对一些锁表操作进行优化。
下面我们举例说明一下，如何通过慢查询日志定位执行效率底的 SQL 语句：

开启慢查询日志 , 配置样例：
```
[mysqld]
log-slow-queries
```
在 `my.cnf` 配置文件中增加上述配置项并重启 `mysql` 服务，这时 mysql 慢查询功能生效。慢查询 日志将写入参数 DATADIR （数据目录）指定的路径下，默认文件名是 `host_name-slow.log` 。


<!--more-->


和错误日志、查询日志一样，慢查询日志记录的格式也是纯文本，可以被直接读取。下例中演示了慢查询日志的设置和读取过程。

（ 1 ）首先查询一下 long_query_time 的值 。
```
mysql> show variables like ‘long%’;

+-—-—-—-—-——+-—-—–+

| Variable_name | Value |

+-—-—-—-—-——+-—-—–+

| long_query_time | 10 |

+-—-—-—-—-——+-—-—–+

1 row in set (0.00 sec)
```
（ 2 ）为了方便测试，将修改慢查询时间为 5 秒。
```
mysql> set long_query_time=5;

Query OK, 0 rows affected (0.02 sec)
```
（ 3 ）依次执行下面两个查询语句。

第一个查询因为查询时间低于 5 秒而不会出现在慢查询日志中：
```
mysql> select count(*) from order2008;

+-—-—-—–+

| count(*) |

+-—-—-—–+

| 208 |

+-—-—-—–+

1 row in set (0.00 sec)
```
第二个查询因为查询时间大于 5 秒而应该出现在慢查询日志中：
```
mysql> select count(*) from t_user;

+-—-—-—–+

| count(*) |

+-—-—-—–+

| 6552961 |

+-—-—-—–+

1 row in set (11.07 sec)
```
（ 4 ）查看慢查询日志。
```
[root@localhost mysql]# more localhost-slow.log

# Time: 081026 19:46:34

# User@Host: root[root] @ localhost []

# Query_time: 11 Lock_time: 0 Rows_sent: 1 Rows_examined: 6552961

select count(*) from t_user;
```
从上面日志中，可以发现查询时间超过 5 秒的 SQL ，而小于 5 秒的则没有出现在此日志中。

如果慢查询日志中记录内容很多，可以使用 mysqldumpslow 工具（ MySQL 客户端安装自带）来对慢查询日志进行分类汇总。下例中对日志文件 mysql_master-slow.log 进行了分类汇总，只显示汇总后摘要结果：
```
[root@mysql_master mysql_data]# mysqldumpslow mysql_master-slow.log

Reading mysql slow query log from mysql_master-slow.log

Count: 2 Time=11.00s (22s) Lock=0.00s (0s) Rows=1.0 (2), root[root]@mysql_master

select count(N) from t_user;
```
对于 SQL 文本完全一致，只是变量不同的语句， `mysqldumpslow` 将会自动视为同一个语句进行统计，变量值用 N 来代替。这个统计结果将大大增加用户阅读慢查询日志的效率，并迅速定位系统的 SQL 瓶颈。

**注意**：慢查询日志对于我们发现应用中有性能问题的 SQL 很有帮助，建议正常情况下，打开此日志并经常查看分析。

本文来源：[http://www.unix-center.net](http://www.unix-center.net)