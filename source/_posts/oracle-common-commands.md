---
title: ORACLE 常用命令
date: 2010-09-24 14:34:00
updated: 2015-09-18 14:35:29
tags: 
- java
- nio
categories: 
- java

---
ORACLE 常用命令 :
 
一、ORACLE的启动和关闭
1、在单机环境下
要想启动或关闭ORACLE系统必须首先切换到ORACLE用户，如下
su - oracle

a、启动ORACLE系统
oracle>svrmgrl
SVRMGR>connect internal
SVRMGR>startup
SVRMGR>quit


<!--more-->


b、关闭ORACLE系统
oracle>svrmgrl
SVRMGR>connect internal
SVRMGR>shutdown
SVRMGR>quit

启动oracle9i数据库命令：
$ sqlplus /nolog

SQL*Plus: Release 9.2.0.1.0 - Production on Fri Oct 31 13:53:53 2003

Copyright (c) 1982, 2002, Oracle Corporation.  All rights reserved.

SQL> connect / as sysdba  
Connected to an idle instance.
SQL> startup^C

SQL> startup
ORACLE instance started.


2、在双机环境下
要想启动或关闭ORACLE系统必须首先切换到root用户，如下
su － root

a、启动ORACLE系统
hareg －y oracle

b、关闭ORACLE系统
hareg －n oracle

Oracle数据库有哪几种启动方式


说明：

有以下几种启动方式：
1、startup nomount
非安装启动，这种方式启动下可执行：重建控制文件、重建数据库

读取init.ora文件，启动instance，即启动SGA和后台进程，这种启动只需要init.ora文件。


2、startup mount dbname
安装启动，这种方式启动下可执行：
数据库日志归档、
数据库介质恢复、
使数据文件联机或脱机，
重新定位数据文件、重做日志文件。

执行“nomount”，然后打开控制文件，确认数据文件和联机日志文件的位置，
但此时不对数据文件和日志文件进行校验检查。


3、startup open dbname
先执行“nomount”，然后执行“mount”，再打开包括Redo log文件在内的所有数据库文件，
这种方式下可访问数据库中的数据。


4、startup，等于以下三个命令
startup nomount
alter database mount
alter database open


5、startup restrict
约束方式启动
这种方式能够启动数据库，但只允许具有一定特权的用户访问
非特权用户访问时，会出现以下提示：
ERROR：
ORA-01035: ORACLE 只允许具有 RESTRICTED SESSION 权限的用户使用


6、startup force
强制启动方式
当不能关闭数据库时，可以用startup force来完成数据库的关闭
先关闭数据库，再执行正常启动数据库命令


7、startup pfile=参数文件名
带初始化参数文件的启动方式
先读取参数文件，再按参数文件中的设置启动数据库
例：startup pfile=E:Oracleadminoradbpfileinit.ora


8、startup EXCLUSIVE
二、用户如何有效地利用数据字典
  　ORACLE的数据字典是数据库的重要组成部分之一，它随着数据库的产生而产生, 随着数据库的变化而变化,
体现为sys用户下的一些表和视图。数据字典名称是大写的英文字符。

    数据字典里存有用户信息、用户的权限信息、所有数据对象信息、表的约束条件、统计分析数据库的视图等。
我们不能手工修改数据字典里的信息。

　　很多时候，一般的ORACLE用户不知道如何有效地利用它。

　　dictionary　　　全部数据字典表的名称和解释，它有一个同义词dict
    dict_column　　 全部数据字典表里字段名称和解释

    如果我们想查询跟索引有关的数据字典时，可以用下面这条SQL语句:

    SQL>select * from dictionary where instr(comments,'index')>0;

    如果我们想知道user_indexes表各字段名称的详细含义，可以用下面这条SQL语句:

    SQL>select column_name,comments from dict_columns where table_name='USER_INDEXES';

    依此类推，就可以轻松知道数据字典的详细名称和解释，不用查看ORACLE的其它文档资料了。

    下面按类别列出一些ORACLE用户常用数据字典的查询使用方法。

    1、用户

            查看当前用户的缺省表空间
            SQL>select username,default_tablespace from user_users;

        查看当前用户的角色
        SQL>select * from user_role_privs;

        查看当前用户的系统权限和表级权限
        SQL>select * from user_sys_privs;
        SQL>select * from user_tab_privs;

    2、表

            查看用户下所有的表
            SQL>select * from user_tables;

            查看名称包含log字符的表
            SQL>select object_name,object_id from user_objects
                where instr(object_name,'LOG')>0;

            查看某表的创建时间
            SQL>select object_name,created from user_objects where object_name=upper('&table_name');

            查看某表的大小
            SQL>select sum(bytes)/(1024*1024) as "size(M)" from user_segments
                where segment_name=upper('&table_name');

            查看放在ORACLE的内存区里的表
            SQL>select table_name,cache from user_tables where instr(cache,'Y')>0;

    3、索引

            查看索引个数和类别
            SQL>select index_name,index_type,table_name from user_indexes order by table_name;

            查看索引被索引的字段
            SQL>select * from user_ind_columns where index_name=upper('&index_name');

            查看索引的大小
            SQL>select sum(bytes)/(1024*1024) as "size(M)" from user_segments
                where segment_name=upper('&index_name');

    4、序列号

            查看序列号，last_number是当前值
            SQL>select * from user_sequences;