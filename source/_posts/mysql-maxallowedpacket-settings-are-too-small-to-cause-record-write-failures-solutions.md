---
title: [解决]mysql max_allowed_packet 设置过小导致记录写入失败
date: 2018-03-22 17:07:00
updated: 2018-04-13 10:04:31
tags: 
- github
- golang
- captcha
categories: 
- go

---
以下方法的第一种方法在有些机子上更改是没有作用的，建议大家使用第二种方法。

mysql根据配置文件会限制server接受的数据包大小。

有时候大的插入和更新会受max_allowed_packet 参数限制，导致写入或者更新失败。
<!--ap-->
查看目前配置
```bash
show VARIABLES like '%max_allowed_packet%';
显示的结果为：
+--------------------+---------+
| Variable_name      | Value   |
+--------------------+---------+
| max_allowed_packet | 1048576 |
+--------------------+---------+  
```
以上说明目前的配置是：1M

<!--more-->


## 修改方法
### 1、修改配置文件

可以编辑my.cnf来修改（windows下my.ini）,在[mysqld]段或者mysql的server配置段进行修改。
```
max_allowed_packet = 20M如果找不到my.cnf可以通过
mysql --help | grep my.cnf去寻找my.cnf文件。
```
linux下该文件在/etc/下。

### 2、在mysql命令行中修改

在mysql 命令行中运行
```bash
set global max_allowed_packet = 2*1024*1024*10
```
然后退出命令行，重启mysql服务，再进入。（这里设置的是20M大小）
```bash
show VARIABLES like '%max_allowed_packet%';
```
查看下max_allowed_packet是否编辑成功

**注意**：该值设置过小将导致单个记录超过限制后写入数据库失败，且后续记录写入也将失败。
