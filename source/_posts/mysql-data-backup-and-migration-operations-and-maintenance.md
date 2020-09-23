---
title: Mysql 数据备份与迁移
date: 2016-06-06 06:00:00
updated: 2019-05-30 16:18:55
tags: 
- emacs
categories: 
- vim

---
## 准备配置

```mysql
set global max_allowed_packet=300000000;
set global net_buffer_length=300000;
set global interactive_timeout=35900000;
set global wait_timeout=35900000;
```
打开配置文件

```bash
vi /etc/my.cnf
```

添加如下

```ini
[mysqldump] 
max_allowed_packet = 2G
```

<!--more-->


## 导出数据

导出数据库

```bash
mysqldump -u root -pMyNewPass4! zhtj > /root/db/zhtj.sql
```

导出单个表

```bash
mysqldump -u root -pMyNewPass4! zhtj tablename> dbname_users.sql
```

## 服务器间复制

从本地复制到远程

复制单个文件

```bash
scp /root/db/zhtj.sql root@172.19.5.147:/data/db/zhtj.sql
```

复制目录

```bash
scp -r /root/db/* root@172.19.5.147:/data/db/
```

## 导入数据

### 方式一

可以显示导入过程

登录

```bash
mysql -uroot -p
```

回车输入密码

创建数据库

```mysql
create database zhtj;
```

选择数据库

```mysql
use zhtj;
```

设置编码

```mysql
set names utf8;
```

导入

```mysql
source zhtj.sql
```

### 方式二

无法显示导入过程

导入数据库

```bash
mysql -u root -p MyNewPass4! zhtj < "/data/db/zhtj.sql"
```

## 查看数据行数

### 方式一 (精确)

生成查询各表条数的SQL

```mysql
select concat(
    'select "',
    TABLE_name,
    '", count(*) from ',
    TABLE_SCHEMA,
    '.',
    TABLE_name,
    ' union '
) from information_schema.tables
where TABLE_SCHEMA='zhtj';
```

把查询出来的结果整体复制后 删除最后一个`union` 整体全选运行即可

### 方式二 (不精确)

> 对于InnoDB表，table_rows行计数仅是大概估计值,所以结果并不精确

查看各表数据行数(按表名排序)

```mysql
use information_schema;
select table_name,table_rows from tables
where TABLE_SCHEMA = 'zhtj'
order by table_name asc;
```

查看各表数据行数(按数据条数排序)

```mysql
use information_schema;
select table_name,table_rows from tables
where TABLE_SCHEMA = 'zhtj'
order by table_rows desc;
```

查看总条数

```mysql
USE information_schema;
SELECT SUM(TABLE_ROWS) FROM TABLES WHERE TABLE_SCHEMA = 'zhtj';
```
