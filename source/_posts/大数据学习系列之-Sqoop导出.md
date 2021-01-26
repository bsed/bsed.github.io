---
title: "大数据学习系列之 Sqoop导出"
categories: [ "大数据/NLP" ]
tags: [ "sqoop","export" ]
draft: false
slug: "large-data-learning-series-sqoop-export"
date: "2016-12-04 10:29:00"
---

## 1、 通用参数

 - –connect，同导入
 - –username，同导入
 - –password，同导入

## 2、导出控制参数

 - `–columnsclass_id,class_name,teacher`注意：没有被包含在–columns后面（例如class_month,last_mod_ts）的这些列名或字段要么具备默认值，要么就允许插入空值，数据库会拒绝接受sqoop导出的数据，导致Sqoop作业失败 
 - `–export-dir`，导出目录，在执行导出的时候，必须指定这个参数，同时需要具备`–table`或`–call`参数两者之一，–table是指的导出数据库当中对应的表，–call是指的某个存储过程
 - `–input-null-string、–input-null-non-string`，这两参数是可选，如果没有指定第一个参数，对于字符串类型的列来说，“NULL”这个字符串就回被翻译成空值，如果没有使用第二个参数，无论是“NULL”字符串还是说空字符串也好，对于非字符串类型的字段来说，这两个类型的空串都会被翻译成空值


<!--more-->


## 3、全表导出
```bash
sqoop export --connect jdbc:mysql://bigdata:3306/sqooptest --username root --password 123456 --table bigdata2 --export-dir /user/root/bigdata/
```
## 4、更新导出

 - `–update-key`，更新标识，即根据某个字段进行更新，例如`class_id`，可以指定多个更新标识的，用逗号分隔
 - `–updatemod`，有两种模式，一种是`updateonly`（默认模式），仅仅更新已存在的数据记录，不会插入新纪录，另一种模式是allowinsert，允许插入新纪录

Sqoop的Export工具，对应两种语句，一种是Insert语句，如果表当中存在PK约束，且表中已包含数据，此时，导出报错。此时需要用到`—update-key`和`updatemod`

如果指定了`update-key`，那么Sqoop就会修改在数据表中已存在的数据，此时的每一个更行数据记录都会变成一个Update语句，用这个语句去更新目标表中已存在的数据，这是根据 `–update-key`所指定的这个列进行更新的。`Update set teacher="kelvin" where class_id = 1`。 

 1. 若`update-key`所指定的字段不是PK字段，若同时`updatemod`使用`updateonly`模式时，就仅进行更新，若`updatemod使`用`allowinsert`模式，那么实质上就是一个**insert**操作
 2. 若update-key所指定的字段是PK字段，同时updatemod是allowinsert时，实质上是一个insert &   
    update的操作，若`updatemod`是`updateonly`时，实质仅为update操作

*示例:*
```bash
# updateonly example
sqoop export --connect jdbc:mysql://bigdata:3306/sqooptest --username root --password 123456 --table bigdata2 --export-dir /user/root/bigdata/ --update-key class_id --update-mode updateonly

# allowinsert example
sqoop export --connect jdbc:mysql://centos:3306/sqooptest --username root --password 123456 --table bigdata2 --export-dir /user/root/bigdata/ --update-key class_id --update-mode allowinsert
```