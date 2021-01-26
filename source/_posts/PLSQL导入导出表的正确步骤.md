---
title: "PLSQL导入导出表的正确步骤"
categories: [ "SQL" ]
tags: [ "oracle","plsql" ]
draft: false
slug: "plsql-import-and-export-table-the-correct-steps"
date: "2010-07-05 13:36:00"
---

## PLSQL导入导出表的正确步骤
原来总是直接 `tools->import talbes->Oracle Import`结果发现有的时候会出错：有的表不能正确导入， baidu+googel解决办法如下：

导出步骤：

 1.  tools ->export user object 选择选项，导出.sql文件
 2.  tools ->export tables-> Oracle Export 选择选项导出.dmp文件

导入步骤：


<!--more-->


 1. tools->import tables->SQL Inserts 导入.sql文件
 2. tools->import talbes->Oracle Import然后再导入dmp文件

数据库的导入导出成功

=======================解释=====================================================================

Tools->Export User Objects导出的是建表语句（包括存储结构）

Tools->Export Tables里面包含三种导出方式：

三种方式都能导出表结构以及数据，网上说三种方法有区别，如下：

Oracle Export，Sql Insert，pl/sql developer 
第一种是导出为.dmp的文件格式，.dmp文件是二进制的，可以跨平台，还能包含权限，效率也很不错，用得最广 
第二种是导出为.sql文件的，可用文本编辑器查看，通用性比较好，但效率不如第一种，适合小数据量导入导出。尤其注意的是表中不能有大字段（blob,clob,long），如果有，会提示不能导出(提示如下： 
table contains one or more LONG columns cannot export in sql format,user Pl/sql developer format instead)，可以用第一种和第三种方式导出。 第三种是导出为.pde格式的，.pde为Pl/sql developer自有的文件格式，只能用Pl/sql developer自己导入导出；不能用编辑器查看。

个人比较中意第二种方法，毕竟是SQL文件嘛，自己还可以看，想改也方便