---
title: "LNMP 关闭 Mysql日志"
categories: [ "Linux" ]
tags: [ "linux","mysql","nginx" ]
draft: false
slug: "lnmp-close-mysql-logli"
date: "2013-07-16 21:39:00"
---

本身mysql会生成一堆的mysql.bin.*的一堆的日志，针对我们这种小网站来说，确实没什么实际大的用处，还有可能塞满我们的硬盘。一般情况下，我都会关了他，反正我有每个小时的`mysqldump`导出记录备档。

找到文件 `/etc/my.cnf` 注释下面2行文件：
```
#log-bin=mysql-bin
#binlog_format=mixed
```
当然，如果你非要留着，也可以缩短日志的保存时间，默认是10天。

```
expire_logs_days = 10 #修改此项即可
```