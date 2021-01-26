---
title: "Deadlock found when trying to get lock; try restarting transaction    以及解决办法"
categories: [ "SQL" ]
tags: [ "mysql","deadlock" ]
draft: false
slug: "794449-stp-thread"
date: "2019-06-13 14:58:00"
---

查看控制台日志，
794449 [STP Thread #43] ERROR WLB2016.Core.LoggingService (null) - Exception occured. MySql.Data.MySqlClient.MySqlException (0x80004005): Deadlock found when trying to get lock; try restarting transaction    在 MySql.Data.MySqlClient.MySqlStream.ReadPacket() 位置 C:\workspace\csharp\cs2018\mysql-connector-net-6.9.9-src\Source\MySql.Data\MySqlStream.cs:行号 163    在 MySql.Data.MySqlClient.NativeDriver.GetResult(Int32& affectedRow, Int64& insertedId) 位置 C:\workspace\csharp\cs2018\mysql-connector-net-6.9.9-src\Source\MySql.Data\NativeDriver.cs:行号 617    在 MySql.Data.MySqlClient.Driver.GetResult(Int32 statementId, Int32& affectedRows, Int64& insertedId) 位置 C:\workspace\csharp\cs2018\mysql-connector-net-6.9.9-src\Source\MySql.Data\Driver.cs:行号 455    在 MySql.Data.MySqlClient.Driver.NextResult(Int32 statementId, Boolean force) 位置 C:\workspace\csharp\cs2018\mysql-connector-net-6.9.9-src\Source\MySql.Data\Driver.cs:行号 444    在 MySql.Data.MySqlClient.MySqlDataReader.NextResult() 位置 C:\workspace\csharp\cs2018\mysql-connector-net-6.9.9-src\Source\MySql.Data\datareader.cs:行号 900    在 MySql.Data.MySqlClient.MySqlCommand.ExecuteReader(CommandBehavior behavior) 位置 C:\workspace\csharp\cs2018\mysql-connector-net-6.9.9-src\Source\MySql.Data\command.cs:行号 551    在 MySql.Data.MySqlClient.MySqlCommand.ExecuteReader() 位置 C:\workspace\csharp\cs2018\mysql-connector-net-6.9.9-src\Source\MySql.Data\command.cs:行号 395    在 MySql.Data.MySqlClient.MySqlCommand.ExecuteNonQuery() 位置 C:\workspace\csharp\cs2018\mysql-connector-net-6.9.9-src\Source\MySql.Data\command.cs:行号 338    在 WLB2016.LocalIntegrationTestProxy.Program.cleanRegisterOrgMemberV2(String orgId, String userId, MySqlConnection pdcConn, String pdcSuffix, String oldPolitcalId, Int32 reasonType) 位置 C:\workspace\csharp\cs2018\zhtjapi\0516UpdateRegisterLeague516V2.cs:行号 378


<!--more-->


相关分析sql
```sql
show status like 'Table%';
show status like 'innodb_row_lock%';
CREATE TABLE innodb_monitor(a INT) ENGINE=INNODB;
select * from innodb_monitor;
show engine innodb status;
show processlist;
```
## mysql deadlock found when trying to get lock 问题排查

### 1 获 取锁等待情况
可以通过检查 table_locks_waited和table_locks_immediate状态变量来分析系统上的表锁定争夺：
```mysql
show status like 'Table%';
```
![88641-94y30kspj2.png](https://imgs.gnux.cn/usr/uploads/2019/06/1494655038.png)

### 2 可以通过检查 Innodb_row_lock状态变量来分析系统上的行锁的争夺情况：
```mysql
show status like 'innodb_row_lock%';
```
![80307-gtk1hfa6wo6.png](https://imgs.gnux.cn/usr/uploads/2019/06/1530451232.png)

### 3 设置监视器
另外，针对Innodb类型的表，如果 需要察看当前的锁等待情况，可以设置InnoDB Monitors，然后通过`Show innodb status`察看，设置的方式是：
```bash
CREATE TABLE innodb_monitor(a INT) ENGINE=INNODB;
```
监视器可以通过发出下列语句来被停止：
DROP TABLE innodb_monitor;

设置监视器后，在`show innodb status`(**高版本使用**：`show engine innodb status`)的显示内容中，会有详细的当前锁等待的信息，包括表名、锁类型、锁定记录的情况等等，便于进行进一步的分析和问题的确定。打开监视器以后，默 认情况下每15秒会向日志中记录监控的内容，如果长时间打开会导致.err文件变得非常的巨大，所以我们在确认问题原因之后，要记得删除监控表以关闭监视 器。或者通过使用--console选项来启动服务器以关闭写日志文件。

### 4 查看自己占用的连接
如果是root帐号，你能看到所有用户的当前连接。如果是其它普通帐号，只能看到自己占用的连接。
`show processlist;`只 列出前100条，如果想全列出请使用`show full processlist;`

```mysql
show processlist;
```
<!--more-->


### 查看死锁情况
```mysql
CREATE TABLE innodb_monitor(a INT) ENGINE=INNODB;
```

```
=====================================
2019-06-13 03:05:07 0x7fa92567d700 INNODB MONITOR OUTPUT
=====================================
Per second averages calculated from the last 61 seconds
-----------------
BACKGROUND THREAD
-----------------
srv_master_thread loops: 1566014 srv_active, 0 srv_shutdown, 21563407 srv_idle
srv_master_thread log flush and writes: 23128571
----------
SEMAPHORES
----------
OS WAIT ARRAY INFO: reservation count 14127267
OS WAIT ARRAY INFO: signal count 28699047
RW-shared spins 0, rounds 19258157, OS waits 5057543
RW-excl spins 0, rounds 158187760, OS waits 3886464
RW-sx spins 1567425, rounds 34097271, OS waits 572634
Spin rounds per wait: 19258157.00 RW-shared, 158187760.00 RW-excl, 21.75 RW-sx
------------------------
LATEST DETECTED DEADLOCK
------------------------
2019-06-13 03:01:38 0x7fa92c1ba700
*** (1) TRANSACTION:
TRANSACTION 184338367, ACTIVE 0 sec starting index read
mysql tables in use 3, locked 3
LOCK WAIT 4 lock struct(s), heap size 1136, 3 row lock(s)
MySQL thread id 304406, OS thread handle 140364564858624, query id 151251708 172.19.13.222 wlb_inner updating
UPDATE OrgMember35 SET DeleteStatus = 202 WHERE DeleteStatus =1 AND IsBelong = 1 AND OrgID = 'Qp56l1zQ' AND UserID = 'k8LZJo4Q'
*** (1) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 129892 page no 18 n bits 736 index OrgID of table `WLB_BDCJiangsu`.`OrgMember35` trx id 184338367 lock_mode X waiting
Record lock, heap no 330 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003b1; asc     ;;

*** (2) TRANSACTION:
TRANSACTION 184338366, ACTIVE 0 sec fetching rows
mysql tables in use 3, locked 3
6 lock struct(s), heap size 1136, 191 row lock(s)
MySQL thread id 304384, OS thread handle 140364566210304, query id 151251707 172.19.13.222 wlb_inner updating
UPDATE OrgMember35 SET DeleteStatus = 202 WHERE DeleteStatus =1 AND IsBelong = 1 AND OrgID = 'Qp56l1zQ' AND UserID = 'xQO5a7VQ'
*** (2) HOLDS THE LOCK(S):
RECORD LOCKS space id 129892 page no 18 n bits 736 index OrgID of table `WLB_BDCJiangsu`.`OrgMember35` trx id 184338366 lock_mode X
Record lock, heap no 330 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003b1; asc     ;;

Record lock, heap no 331 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003b2; asc     ;;

Record lock, heap no 332 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003b3; asc     ;;

Record lock, heap no 333 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003b4; asc     ;;

Record lock, heap no 334 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003b5; asc     ;;

Record lock, heap no 335 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003b6; asc     ;;

Record lock, heap no 336 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003b7; asc     ;;

Record lock, heap no 337 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003b8; asc     ;;

Record lock, heap no 338 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003b9; asc     ;;

Record lock, heap no 339 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003ba; asc     ;;

Record lock, heap no 340 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003bb; asc     ;;

Record lock, heap no 341 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003bc; asc     ;;

Record lock, heap no 342 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003bd; asc     ;;

Record lock, heap no 343 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003be; asc     ;;

Record lock, heap no 344 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003bf; asc     ;;

Record lock, heap no 345 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003c0; asc     ;;

Record lock, heap no 346 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003c1; asc     ;;

Record lock, heap no 347 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003c2; asc     ;;

Record lock, heap no 348 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003c3; asc     ;;

Record lock, heap no 349 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003c4; asc     ;;

Record lock, heap no 350 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003c5; asc     ;;

Record lock, heap no 351 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003c6; asc     ;;

Record lock, heap no 352 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003c7; asc     ;;

Record lock, heap no 353 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003c8; asc     ;;

Record lock, heap no 354 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003c9; asc     ;;

Record lock, heap no 355 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003ca; asc     ;;

Record lock, heap no 356 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003cb; asc     ;;

Record lock, heap no 357 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003cc; asc     ;;

Record lock, heap no 358 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003cd; asc     ;;

Record lock, heap no 359 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003ce; asc     ;;

Record lock, heap no 360 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003cf; asc     ;;

Record lock, heap no 361 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003d0; asc     ;;

Record lock, heap no 362 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003d1; asc     ;;

Record lock, heap no 363 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003d2; asc     ;;

Record lock, heap no 364 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003d3; asc     ;;

Record lock, heap no 365 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003d4; asc     ;;

Record lock, heap no 366 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003d5; asc     ;;

Record lock, heap no 367 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003d6; asc     ;;

Record lock, heap no 368 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003d7; asc     ;;

Record lock, heap no 441 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003d8; asc     ;;

Record lock, heap no 442 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003d9; asc     ;;

Record lock, heap no 443 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003da; asc     ;;

Record lock, heap no 444 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003db; asc     ;;

Record lock, heap no 445 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003dc; asc     ;;

Record lock, heap no 446 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003dd; asc     ;;

Record lock, heap no 447 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003de; asc     ;;

Record lock, heap no 448 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003df; asc     ;;

Record lock, heap no 449 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003e0; asc     ;;

Record lock, heap no 450 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003e1; asc     ;;

Record lock, heap no 451 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003e2; asc     ;;

Record lock, heap no 452 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003e3; asc     ;;

Record lock, heap no 453 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003e4; asc     ;;

Record lock, heap no 454 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003e5; asc     ;;

Record lock, heap no 455 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003e6; asc     ;;

Record lock, heap no 456 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003e7; asc     ;;

Record lock, heap no 457 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003e8; asc     ;;

Record lock, heap no 458 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003e9; asc     ;;

Record lock, heap no 459 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003ea; asc     ;;

Record lock, heap no 460 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003eb; asc     ;;

Record lock, heap no 461 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003ec; asc     ;;

Record lock, heap no 462 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003ed; asc     ;;

Record lock, heap no 463 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003ee; asc     ;;

Record lock, heap no 464 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003ef; asc     ;;

Record lock, heap no 465 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003f0; asc     ;;

Record lock, heap no 466 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003f1; asc     ;;

Record lock, heap no 467 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003f2; asc     ;;

Record lock, heap no 468 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003f3; asc     ;;

Record lock, heap no 469 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003f4; asc     ;;

Record lock, heap no 470 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003f5; asc     ;;

Record lock, heap no 471 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003f6; asc     ;;

Record lock, heap no 472 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003f7; asc     ;;

Record lock, heap no 473 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003f8; asc     ;;

Record lock, heap no 474 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003f9; asc     ;;

Record lock, heap no 475 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003fa; asc     ;;

Record lock, heap no 476 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003fb; asc     ;;

Record lock, heap no 477 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003fc; asc     ;;

Record lock, heap no 478 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003fd; asc     ;;

Record lock, heap no 479 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003fe; asc     ;;

Record lock, heap no 480 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 000003ff; asc     ;;

Record lock, heap no 481 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 00000400; asc     ;;

Record lock, heap no 482 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 00000401; asc     ;;

Record lock, heap no 483 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 00000402; asc     ;;

Record lock, heap no 484 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 00000403; asc     ;;

Record lock, heap no 485 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 00000404; asc     ;;

Record lock, heap no 486 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 00000405; asc     ;;

Record lock, heap no 487 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 00000406; asc     ;;

Record lock, heap no 488 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 00000407; asc     ;;

Record lock, heap no 489 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 00000408; asc     ;;

Record lock, heap no 490 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 00000409; asc     ;;

Record lock, heap no 491 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 0000040a; asc     ;;

Record lock, heap no 492 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 0000040b; asc     ;;

Record lock, heap no 493 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 0000040c; asc     ;;

Record lock, heap no 494 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 0000040d; asc     ;;

Record lock, heap no 495 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 1: len 4; hex 0000040e; asc     ;;

*** (2) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 129892 page no 20 n bits 200 index PRIMARY of table `WLB_BDCJiangsu`.`OrgMember35` trx id 184338366 lock_mode X locks rec but not gap waiting
Record lock, heap no 3 PHYSICAL RECORD: n_fields 32; compact format; info bits 0
 0: len 4; hex 0000040e; asc     ;;
 1: len 6; hex 00000afa6ca3; asc     l ;;
 2: len 7; hex 71000002081f15; asc q      ;;
 3: len 8; hex 517035366c317a51; asc Qp56l1zQ;;
 4: len 8; hex 6b384c5a4a6f3451; asc k8LZJo4Q;;
 5: len 3; hex 8faea1; asc    ;;
 6: len 9; hex e6b288e4bb99e8998e; asc          ;;
 7: len 1; hex 01; asc  ;;
 8: len 3; hex 8f918b; asc    ;;
 9: len 18; hex 353332313238313939323132313130353138; asc 532128199212110518;;
 10: len 11; hex 3133323934393133323333; asc 13294913233;;
 11: len 1; hex 81; asc  ;;
 12: len 1; hex 80; asc  ;;
 13: len 1; hex 80; asc  ;;
 14: len 1; hex 80; asc  ;;
 15: len 1; hex 00; asc  ;;
 16: len 6; hex 303147515459; asc 01GQTY;;
 17: SQL NULL;
 18: len 1; hex 00; asc  ;;
 19: SQL NULL;
 20: SQL NULL;
 21: SQL NULL;
 22: SQL NULL;
 23: len 4; hex 5d01c0e5; asc ]   ;;
 24: len 2; hex 03e8; asc   ;;
 25: SQL NULL;
 26: SQL NULL;
 27: SQL NULL;
 28: len 1; hex 80; asc  ;;
 29: len 1; hex 80; asc  ;;
 30: len 4; hex 000000cc; asc     ;;
 31: len 1; hex 80; asc  ;;

*** WE ROLL BACK TRANSACTION (1)
------------
TRANSACTIONS
------------
Trx id counter 184338534
Purge done for trx's n:o < 184338458 undo n:o < 0 state: running but idle
History list length 43
LIST OF TRANSACTIONS FOR EACH SESSION:
---TRANSACTION 421840093819472, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093818560, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093817648, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093816736, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093815824, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093814000, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093813088, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093812176, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093811264, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093810352, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093809440, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093807616, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093806704, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093805792, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093804880, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093803056, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093802144, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093801232, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093797584, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093796672, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093795760, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093793024, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093792112, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093791200, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093790288, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093789376, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093788464, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093786640, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093782080, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093780256, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093777520, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093814912, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093808528, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093803968, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093800320, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093799408, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093798496, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093794848, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093793936, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093784816, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093783904, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093782992, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093779344, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093778432, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093775696, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093781168, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093776608, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093787552, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 421840093785728, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
--------
FILE I/O
--------
I/O thread 0 state: waiting for completed aio requests (insert buffer thread)
I/O thread 1 state: waiting for completed aio requests (log thread)
I/O thread 2 state: waiting for completed aio requests (read thread)
I/O thread 3 state: waiting for completed aio requests (read thread)
I/O thread 4 state: waiting for completed aio requests (read thread)
I/O thread 5 state: waiting for completed aio requests (read thread)
I/O thread 6 state: waiting for completed aio requests (write thread)
I/O thread 7 state: waiting for completed aio requests (write thread)
I/O thread 8 state: waiting for completed aio requests (write thread)
I/O thread 9 state: waiting for completed aio requests (write thread)
Pending normal aio reads: [0, 0, 0, 0] , aio writes: [0, 0, 0, 0] ,
 ibuf aio reads:, log i/o's:, sync i/o's:
Pending flushes (fsync) log: 0; buffer pool: 0
68776160 OS file reads, 116895808 OS file writes, 74172757 OS fsyncs
0.05 reads/s, 16384 avg bytes/read, 0.00 writes/s, 0.00 fsyncs/s
-------------------------------------
INSERT BUFFER AND ADAPTIVE HASH INDEX
-------------------------------------
Ibuf: size 1, free list len 222, seg size 224, 818266 merges
merged operations:
 insert 36093989, delete mark 3376788, delete 1330278
discarded operations:
 insert 0, delete mark 0, delete 0
Hash table size 34673, node heap has 13 buffer(s)
Hash table size 34673, node heap has 162 buffer(s)
Hash table size 34673, node heap has 8 buffer(s)
Hash table size 34673, node heap has 166 buffer(s)
Hash table size 34673, node heap has 14 buffer(s)
Hash table size 34673, node heap has 157 buffer(s)
Hash table size 34673, node heap has 6 buffer(s)
Hash table size 34673, node heap has 178 buffer(s)
456.27 hash searches/s, 12.56 non-hash searches/s
---
LOG
---
Log sequence number 261597556186
Log flushed up to   261597556186
Pages flushed up to 261597556186
Last checkpoint at  261597556177
0 pending log flushes, 0 pending chkp writes
67334857 log i/o's done, 0.00 log i/o's/second
----------------------
BUFFER POOL AND MEMORY
----------------------
Total large memory allocated 137428992
Dictionary memory allocated 6691340
Buffer pool size   8191
Free buffers       1021
Database pages     6466
Old database pages 2368
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 41610699, not young 7099315146
0.02 youngs/s, 0.10 non-youngs/s
Pages read 68749986, created 14626986, written 45132741
0.05 reads/s, 0.00 creates/s, 0.00 writes/s
Buffer pool hit rate 1000 / 1000, young-making rate 0 / 1000 not 0 / 1000
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 6466, unzip_LRU len: 0
I/O sum[0]:cur[3], unzip sum[0]:cur[0]
--------------
ROW OPERATIONS
--------------
0 queries inside InnoDB, 0 queries in queue
0 read views open inside InnoDB
Process ID=2125, Main thread ID=140364734768896, state: sleeping
Number of rows inserted 1837124223, updated 64711422, deleted 2966563, read 22059658267
0.00 inserts/s, 0.00 updates/s, 0.00 deletes/s, 699.14 reads/s
----------------------------
END OF INNODB MONITOR OUTPUT
============================
```
从上面可以发现用户 `wib_inner` 在更新表`OrgMember35`的时候发生死锁。

## 解决方案（2个sql更新不同记录但死锁）：


### 问题起因：
两条写sql，操作的记录没有任何冲突，但发生死锁

预备知识：

InnoDB行锁是通过给索引上的索引项加锁来实现的

创建测试表
```mysql
CREATE TABLE `t1` (

  `pk_id` INT(11) NOT NULL,

  `type` INT(11) NOT NULL,

  `status` INT(11) NOT NULL,

  PRIMARY KEY (`pk_id`)

);

create index idx_type on t1(type);
create index idx_status on t1(status);
```
生成测试数据
```
INSERT INTO  t1 (pk_id,TYPE,STATUS)

VALUES

(1,1,0),

(2,1,0),

(3,1,0),

(4,2,0),

(5,2,0),

(6,1,1),

(7,1,1),

(8,2,1);
```

例1.不一样的锁等待

连接A执行
```mysql
SET autocommit=0;
BEGIN;
SELECT * FROM t1 force index(PRIMARY) WHERE pk_id<4 AND TYPE=1 AND pk_id!=2 FOR UPDATE;
```
马上返回查到的结果有两条pk_id为1和3

连接B执行
```mysql
SET autocommit=0;
BEGIN;
SELECT * FROM t1 WHERE pk_id=2 FOR UPDATE;
```
执行后连接B一直是等待状态，如果连接A commit，连接B马上就执行完成

说明：连接A虽然查出来的结果只有pk_id为1和3的两条记录，但把pk_id为2的PRIMARY索引记录也锁住了，所以连接B一直等待

换个索引试试

在连接A里
```mysql
commit;
SELECT * FROM t1 force index(idx_type) WHERE pk_id<4 AND TYPE=1 AND pk_id!=2 FOR UPDATE;
```
注意只换了force index使用的索引，其他都没变

在连接B里想写操作TYPE=1的记录(pk_id为1、2、3、6、7)都等待，因为连接A把idx_type中TYPE=1的记录都锁了

和之前例子对照可以发现，索引锁是按使用的索引来操作，并且可以确定的是锁的范围会超出查询结果范围，这点和一般以为的不一样，具体算法还有待研究。

例2.死锁

连接A执行
```mysql
COMMIT;
SET autocommit=0;
SELECT * FROM t1 WHERE pk_id<5 FOR UPDATE;
```
连接A先锁住了pk索引的部分记录

接着连接B执行
```mysql
COMMIT;
SET autocommit=0;
SELECT * FROM t1 FORCE INDEX (idx_status) WHERE STATUS=0 FOR UPDATE;
```
连接B锁往了idx_status的部分记录，再要锁pk时被连接A block，所以只能等待

最后连接A执行
```mysql
UPDATE t1 SET STATUS=6 WHERE pk_id<5;
```
这时连接B报dead lock found

简单来讲连接A先锁住pk，B先锁住idx_status再拿pk就拿不到，这时A再拿idx_status就死锁了

类似于一个人有X但要Y，一个人有Y但要X，互不相让，就死锁了。

例3.想不到的死锁

把例1和例2的情况结合起来，就会出来本文最开始碰到的问题，想不到的死锁，即更新的记录完全不冲突，但就是死锁了

比如
```mysql
SELECT * FROM t1 force index(idx_type) WHERE pk_id<4 AND TYPE=1 FOR UPDATE;
```
和
```sql
update t1 set status=1 where pk_id=6
```
虽然想操作的记录不同，但锁的记录有相同的，所以也可能会死锁

例4.index merge死锁

如果sql where里同时使用了type和status，因为type和status上都有单字段索引，所以explain会发现使用了index merge

有的sql使用的索引是先idx_type再idx_status，有的先idx_status再idx_type

这样如果锁的记录有冲突，就可能和例3一样死锁了


**解决方案**：

1.只有一个pk，不要其他索引。这样只有lock wait，不会死锁
2.有多个index，但写数据时使用的都是同样的index组合
3.有多个index，按不同的index组合写数据，但逻辑上保证锁的记录不冲突

时间所限，只整理了大概的逻辑，一些细节未深入。有兴趣的可以看看mysql的next-key locking

笔者最后使用查询出来的主键解决本项目问题 ~
详细见： 

 - [https://kanbol.iteye.com/blog/1881223](https://kanbol.iteye.com/blog/1881223)
 - [https://www.cnblogs.com/rwxwsblog/p/6655552.html?tdsourcetag=s_pctim_aiomsg](https://www.cnblogs.com/rwxwsblog/p/6655552.html?tdsourcetag=s_pctim_aiomsg)