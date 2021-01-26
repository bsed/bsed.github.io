---
title: "ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (111)"
categories: [ "SQL" ]
tags: [ "mysql","2002" ]
draft: false
slug: "error-2002-hy000-cant-connect-to-local-mysql-server-through-socket-varlibmysqlmysqlsock-111"
date: "2020-04-19 10:31:00"
---

## 一问题发现
命令行进入数据库实后，对数据库中的某个表进行`show`操作，发现如下报错。

```mysql
mysql> show databases;
No connection. Trying to reconnect...
Connection id:    2
Current database: WLB_BDCJiangsu

ERROR 2006 (HY000): MySQL server has gone away
No connection. Trying to reconnect...
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (111)
ERROR:
Can't connect to the server
```


<!--more-->


## 问题定位

上述这种错误常见是MySQL实例关闭或者非正常宕机、MySQL连接超时、MySQL请求线程被kill。笔者通过shell(用`mysql -uroot -pMyNewPass`连接数据库) 打开指定的数据库表时，发现报错（ERROR 2006 (HY000): MySQL server has gone away），通过命令行界面进入其他数据库，执行数据库命令都正常，问题数据库连最基本的数据库变量和态值都无法show。

这里排查走偏了，按照网上的教程对 `My.ini` 做了些优化，发现问题依然存在。但是比较难理解的是通过DBevaer数据库客户端连接，却可以通过`select * from WLB_BDCJiangsu.OrgXXX;`命令查询到数据，笔者怀疑是数据库中的某个表损坏影响的，具体哪一个，目前感觉是不好确定了，之前一直以为是OrgXXX表损坏了，但是现在却可以查询到数据，郁闷啊。。。

查看日志发现实例存在异常shutdown和崩溃恢复记录，但还不能确定具体的原因，可以明确的是单个库存在问题，可以从其他途径去恢复。但是DBA存在即有价值，我们可以尽可能的先尝试以最小的代价解决问题。
虽之前遇到过多次硬件故障导致的数据文件损坏，可以通过集群的其他实例和备份完成恢复并不会很大影响业务，也遇到过自己测试发现单个库文件坏得很彻底，但能通过dump出来数据文件进行恢复。

解决问题后追溯问题，发现日志记录如下故障点（这个日志比较久远，当问题来临时可能没有那么多时间给予分析，需要快速定位初步问题并解决，解决问题的时候不一定能发现这个重要的排错依据，放上日志只是仅供参考和回溯故障原因）。
**网友的观点:**
### 观点一
现这种问题有可能是两种情况
1.查询结果包含海量数据。长时间检索无响应。这种情况，需要优化SQL，尽量减少数据输出量。
2.表结构损坏。可以通过 CHECK TABLE REPAIR TABLE 语句进行检测或修复。
``
### 观点二

使用Navicat for mysql打开表时，出现1030-Got error 28 from storage engine错误。
查询日志：发现**根目录空间已满**或则**mysql默认的临时目录空间不足**，导致无法执行此sql语句。
具体可以通过以下方法确认服务器空间是否已经满了。

命令行执行下：`show variables like '%dir%';`,

basedir | /usr/local/mysql |
| binlog_direct_non_transactional_updates | OFF |
| character_sets_dir | /usr/local/mysql/share/charsets/ |
| datadir | /xx/mysql/var/ |
| innodb_data_home_dir | /xxG/msql/var |
| innodb_log_group_home_dir | /xx/mysql/var |
| innodb_max_dirty_pages_pct | 75 |
| lc_messages_dir | /usr/local/mysql/share/ |
| plugin_dir | /usr/local/mysql/lib/plugin/ |
| slave_load_tmpdir | /tmp |
| tmpdir | /tmp

可以看出mysql当前的临时目录为`/tmp`,然后`df -hl`或`du -sh /tmp`查看该目录是否还有空间，结果的确是空间不足了，所以修改配置文件`/etc/my.cnf`,在【mysqld】下添加`tmpdir = /xx/tmp`。保存，重启，再次导出不是问题了。

**注意**：有时修改配置文件，比如`/etc/my.cnf`，wq保存时会提示 `E514: write error (file system full?)`，有可能/etc/所在的目录的磁盘空间也没了。

**解决方法**：
1.清理根本目录下的日志（应用、数据库）及文件。(例如mysql 默认的temp 目录是否已经满了)
2.mysql数据量太大，新加分区，重新配置mysql目录。

ERROR 1030 (HY000) at line 31: Got error 168 from storage engine （**此观点笔者未尝试已经解决问题了**）
**解决：**
删除数据库所在文件夹下的
ib_buffer_pool
ib_logfile0
ib_logfile1
ibdata1
ibtmp1
机器名.pid
文件，再重新导入即可。

```
2020-04-19T02:41:13.653869Z 0 [Note] InnoDB: Uses event mutexes
2020-04-19T02:41:13.653878Z 0 [Note] InnoDB: GCC builtin __atomic_thread_fence() is used for memory barrier
2020-04-19T02:41:13.653887Z 0 [Note] InnoDB: Compressed tables use zlib 1.2.3
2020-04-19T02:41:13.653905Z 0 [Note] InnoDB: Using Linux native AIO
2020-04-19T02:41:13.656874Z 0 [Note] InnoDB: Number of pools: 1
2020-04-19T02:41:13.657080Z 0 [Note] InnoDB: Using CPU crc32 instructions
2020-04-19T02:41:13.659679Z 0 [Note] InnoDB: Initializing buffer pool, total size = 128M, instances = 1, chunk size = 128M
2020-04-19T02:41:13.675743Z 0 [Note] InnoDB: Completed initialization of buffer pool
2020-04-19T02:41:13.678892Z 0 [Note] InnoDB: If the mysqld execution user is authorized, page cleaner thread priority can be changed. See the man page of setpriority().
2020-04-19T02:41:13.691752Z 0 [Note] InnoDB: Highest supported file format is Barracuda.
2020-04-19T02:41:13.694017Z 0 [Note] InnoDB: Log scan progressed past the checkpoint lsn 395818411968
2020-04-19T02:41:13.694042Z 0 [Note] InnoDB: Doing recovery: scanned up to log sequence number 395818411977
2020-04-19T02:41:13.694054Z 0 [Note] InnoDB: Database was not shutdown normally!
2020-04-19T02:41:13.694064Z 0 [Note] InnoDB: Starting crash recovery.
2020-04-19T02:41:14.918056Z 0 [ERROR] InnoDB: Checksum mismatch in datafile: ./WLB_BDCHenan/OrgVitalityStatProc.ibd, Space ID:7159917, Flags: 33. Please refer to http://dev.mysql.com/doc/refman/5.7/en/innodb-troubleshooting-datadict.html for how to resolve the issue.
2020-04-19T02:41:14.918154Z 0 [ERROR] InnoDB: Operating system error number 2 in a file operation.
2020-04-19T02:41:14.918169Z 0 [ERROR] InnoDB: The error means the system cannot find the path specified.
2020-04-19T02:41:14.918176Z 0 [ERROR] InnoDB: If you are installing InnoDB, remember that you must create directories yourself, InnoDB does not create them.
2020-04-19T02:41:14.918182Z 0 [ERROR] InnoDB: Could not find a valid tablespace file for `WLB_BDCHenan/OrgVitalityStatProc`. Please refer to http://dev.mysql.com/doc/refman/5.7/en/innodb-troubleshooting-datadict.html for how to resolve the issue.
2020-04-19T02:41:14.918196Z 0 [Warning] InnoDB: Ignoring tablespace `WLB_BDCHenan/OrgVitalityStatProc` because it could not be opened.
2020-04-19T02:41:20.009911Z 0 [Note] InnoDB: Removed temporary tablespace data file: "ibtmp1"
2020-04-19T02:41:20.009949Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
2020-04-19T02:41:20.010042Z 0 [Note] InnoDB: Setting file './ibtmp1' size to 12 MB. Physically writing the file full; Please wait ...
2020-04-19T02:41:20.160224Z 0 [Note] InnoDB: File './ibtmp1' size is now 12 MB.
2020-04-19T02:41:20.202709Z 0 [Note] InnoDB: 96 redo rollback segment(s) found. 96 redo rollback segment(s) are active.
2020-04-19T02:41:20.202753Z 0 [Note] InnoDB: 32 non-redo rollback segment(s) are active.
2020-04-19T02:41:20.203778Z 0 [Note] InnoDB: Waiting for purge to start
2020-04-19T02:41:20.253953Z 0 [Note] InnoDB: 5.7.23 started; log sequence number 395818411977
2020-04-19T02:41:20.254032Z 0 [Note] InnoDB: page_cleaner: 1000ms intended loop took 6576ms. The settings might not be optimal. (flushed=0 and evicted=0, during the time.)
2020-04-19T02:41:20.254125Z 0 [Note] InnoDB: Loading buffer pool(s) from /var/lib/mysql/ib_buffer_pool
2020-04-19T02:41:20.255531Z 0 [Note] Plugin 'FEDERATED' is disabled.
2020-04-19T02:41:20.256346Z 0 [Note] InnoDB: Buffer pool(s) load completed at 200419 10:41:20
2020-04-19T02:41:20.259926Z 0 [Note] Found ca.pem, server-cert.pem and server-key.pem in data directory. Trying to enable SSL support using them.
2020-04-19T02:41:20.260230Z 0 [Warning] CA certificate ca.pem is self signed.
2020-04-19T02:41:20.262267Z 0 [Note] Server hostname (bind-address): '*'; port: 3306
2020-04-19T02:41:20.262318Z 0 [Note] IPv6 is available.
2020-04-19T02:41:20.262330Z 0 [Note]   - '::' resolves to '::';
2020-04-19T02:41:20.262366Z 0 [Note] Server socket created on IP: '::'.
2020-04-19T02:41:20.290211Z 0 [Warning] 'user' entry 'root@localhost' ignored in --skip-name-resolve mode.
2020-04-19T02:41:20.290258Z 0 [Warning] 'user' entry 'mysql.session@localhost' ignored in --skip-name-resolve mode.
2020-04-19T02:41:20.290272Z 0 [Warning] 'user' entry 'mysql.sys@localhost' ignored in --skip-name-resolve mode.
2020-04-19T02:41:20.290303Z 0 [Warning] 'user' entry 'wlb_inner@localhost' ignored in --skip-name-resolve mode.
2020-04-19T02:41:20.290334Z 0 [Warning] 'db' entry 'performance_schema mysql.session@localhost' ignored in --skip-name-resolve mode.
2020-04-19T02:41:20.290343Z 0 [Warning] 'db' entry 'sys mysql.sys@localhost' ignored in --skip-name-resolve mode.
2020-04-19T02:41:20.290359Z 0 [Warning] 'proxies_priv' entry '@ root@localhost' ignored in --skip-name-resolve mode.
2020-04-19T02:41:20.292150Z 0 [Warning] 'tables_priv' entry 'user mysql.session@localhost' ignored in --skip-name-resolve mode.
2020-04-19T02:41:20.292173Z 0 [Warning] 'tables_priv' entry 'sys_config mysql.sys@localhost' ignored in --skip-name-resolve mode.
2020-04-19T02:41:20.300760Z 0 [Note] Event Scheduler: Loaded 0 events
2020-04-19T02:41:20.301068Z 0 [Note] /usr/sbin/mysqld: ready for connections.
Version: '5.7.23'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  MySQL Community Server (GPL)
```

## 问题解决

遇到数据文件损坏导致的数据无法正常存取，通常解决的办法是通过备份进行恢复，包括对坏点进行备份恢复。尝试过才知道有思路是好的，但是实践起来不一定容易，果不其然当我想通过dump备份数据再尝试修复的时候出现了错误 MySQL server has gone away。遇到好的问题就要分享，往往问题比较宽泛不好定位的时候容易忽略正确的处理方向。通过好朋友圈的提醒，发现use库的时候输出了-A选项，查询得知可以不加载元数据信息就能进入数据库。

-A选项

当我们打开数据库，即use dbname时，要预读数据库信息。由于数据库太大，即数据库中表非常多，所以如果预读数据库信息，将非常慢，所以就卡住了，如果数据库中表非常少，将不会出现问题

幸运的是通过不预读数据可以正常查看当前数据库所有表、系统变量值和状态值，然后尝试通过对InnoDB和MyISAM表进行批量修复，不过在此应该通过select...into的方式做好数据备份，这里因为是测试环境且有相应的冗余环境，就没做备份处理再修复。通过如下命令查询所有的base表并拼接SQL语句，果然发现了无法修复的坏表，印证了MySQL错误日志的信息。

```mysql
##批量修复MyISAM表
select concat('repair table ',table_name,';') from information_schema.tables where table_schema = 'xx_xxxx' and table_type = 'BASE TABLE' and engine = 'MyISAM';
 
#批量修复InnoDB表
select concat('optimize table ',table_name,';') from information_schema.tables where table_schema = 'xx_xxxx' and table_type = 'BASE TABLE' and engine = 'InnoDB';
```
会生成如下SQL；
```sql
optimize table xx_xxxxxx_fans_person;  
```

- `xx_xxxx`: 代表 数据库名字

通过上述命令发现修复结果不OK的表，并通过查看表行数确认数据已无法导出，删除相应的坏表并重新建立新表（drop table可能出现表不存在或者建表1068错误），导入最近的一次数据备份，重启MySQL实例，发现问题解决，问题库可以正常访问。(鉴于公司保密原则，以下SQL参考引用网友的，自行分析)

**大致原则均是**：（删除错误表，重建立新表，如有错误自己排查原因，解决即可）

1、删除错误表 xx_xxxxxx_fans_person
2、重建表
```sql
mysql>   CREATE TABLE `xx_xxxxxx_fans_person` (
    ->   `person_id` int(20) NOT NULL AUTO_INCREMENT,
    ->   `person_circle_id` int(20) NOT NULL,
    ->   `person_user_id` int(20) NOT NULL,
    ->   `person_time` datetime NOT NULL,
    ->   `type` int(4) DEFAULT '1' COMMENT '1. 组长 2. 成员',
    ->   `merchant_id` int(11) DEFAULT '0',
    ->   `leave_type` int(11) DEFAULT '0' COMMENT '请假状态 0.未请假 1.请假',
    ->   `leave_start_time` datetime DEFAULT NULL COMMENT '请假开始时间',
    ->   `leave_end_time` datetime DEFAULT NULL COMMENT '请假结束时间',
    ->   `is_invalid` int(10) DEFAULT '0' COMMENT '是否失效 0有效 1失效',
    ->   `invalid_id` int(10) DEFAULT '0' COMMENT '失效记录关联ID ',
    ->   PRIMARY KEY (`person_id`),
    ->   KEY `person_circle_id` (`person_circle_id`) USING BTREE,
    ->   KEY `person_user_id` (`person_user_id`) USING BTREE
    -> ) ENGINE=InnoDB;
ERROR 1030 (HY000): Got error 168 from storage engine
```
3、重启

```sql
mysql> select count(*) from xx_xxxxxx_fans_person;
ERROR 1812 (HY000): Tablespace is missing for table `xx_xxxxxx`.`xx_xxxxxx_fans_person`.

mysql> drop table xx_xxxxxx_fans_person;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TABLE `xx_xxxxxx_fans_person` (
    ->   `person_id` int(20) NOT NULL AUTO_INCREMENT,
    ->   `person_circle_id` int(20) NOT NULL,
    ->   `person_user_id` int(20) NOT NULL,
    ->   `person_time` datetime NOT NULL,
    ->   `type` int(4) DEFAULT '1' COMMENT '1. 组长 2. 成员',
    ->   `merchant_id` int(11) DEFAULT '0',
    ->   `leave_type` int(11) DEFAULT '0' COMMENT '请假状态 0.未请假 1.请假',
    ->   `leave_start_time` datetime DEFAULT NULL COMMENT '请假开始时间',
    ->   `leave_end_time` datetime DEFAULT NULL COMMENT '请假结束时间',
    ->   `is_invalid` int(10) DEFAULT '0' COMMENT '是否失效 0有效 1失效',
    ->   `invalid_id` int(10) DEFAULT '0' COMMENT '失效记录关联ID ',
    ->   PRIMARY KEY (`person_id`),
    ->   KEY `person_circle_id` (`person_circle_id`) USING BTREE,
    ->   KEY `person_user_id` (`person_user_id`) USING BTREE
    -> ) ENGINE=InnoDB;
ERROR 1813 (HY000): Tablespace '`xx_xxxxxx`.`xx_xxxxxx_fans_person`' exists.
```
## 总结

1、数据库需要定时备份，防止硬件或者其他问题导致的数据文件损坏
2、先分析问题，排查基本的不可能点，必需查看日志分析问题，注意查看命令报错的输出提示信息（可能帮助我们排查或者修复）
3、可使用-A选项不加载数据库信息尝试进行表修复，提前做好备份

## ERROR 1030 (HY000) at line 33: Got error 168 from storage engine

全库导入出现：ERROR 1030 (HY000) at line 33: Got error 168 from storage engine

报错提示，大概意思是导入的数据库引擎与我的库不匹配．

一下是个人解决方法．

1.修改mysql配置文件.

`vim /etc/mysql/my.cnf`
```conf
default-storage-engine=innodb   #我的数据库存储引擎是innodb   我现在配置默认创建都为innodb
innodb_force_recovery=0           #默认是没有这个参数，没有的话，他的默认值是0，这个参数的值如果大于0，innodb会被禁止使用insert、update、delete命令
max_allowed_packet=1024M   #这个值要大于你导入的文件大小．
```

### innodb_force_recovery参数说明
MySQL数据库当innodb表空间损坏时（如ibdata1文件损坏），尝试启动Mysql服务失败。这个时候可以使用innodb_force_recovery参数进行强制启动

innodb_force_recovery解释：
innodb_force_recovery影响整个InnoDB存储引擎的恢复状况，默认值为0，表示当需要恢复时执行所有的恢复操作
当不能进行有效的恢复操作时，Mysql有可能无法启动，并记录下错误日志。

innodb_force_recovery可以设置为1-6，大的数字包含前面所有数字的影响。
当该参数的数值设置大于0后，可以对表进行select，create，drop操作，但insert，update或者delete这类操作是不允许的。
 
innodb_force_recovery=0   表示当需要恢复时执行所有的恢复操作；
innodb_force_recovery=1   表示忽略检查到的corrupt页；
innodb_force_recovery=2   表示阻止主线程的运行，如主线程需要执行full purge操作，会导致crash；
innodb_force_recovery=3   表示不执行事务回滚操作；
innodb_force_recovery=4   表示不执行插入缓冲的合并操作；
innodb_force_recovery=5   表示不查看重做日志，InnoDB存储引擎会将未提交的事务视为已提交；
innodb_force_recovery=6   表示不执行前滚的操作，强制重启！

恢复记录：
1) 在主从库的Mysql配置文件my.cnf中添加
`vim /etc/my.cnf`
```conf
innodb_force_recovery=6 
```
2) 启动mysql服务
```bash
service mysqld start
```
3）如果启动成功后，再将my.cnf文件中的"innodb_force_recovery=6"配置去掉，然后再次尝试mysql服务的重启，应该OK。
 
在主从库出现这种情况时，如果配置文件里之前就有这个参数，则尝试将该参数值修改为0或6，依次尝试重启。

### 2.重启mysql服务
```bash
systemctl  restart mysql
```

参考：

- [https://www.cnblogs.com/liyingxiao/p/11101990.html](https://www.cnblogs.com/liyingxiao/p/11101990.html)
- [Mysqldump报错和常用参数](https://www.cnblogs.com/wangchengshi/p/12364465.html)
- [数据恢复新姿势——通过ibd和frm文件恢复数据](https://zhuanlan.zhihu.com/p/52185155)