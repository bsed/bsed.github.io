---
title:  MySQL Replace INTO的使用
date: 2008-09-15 08:19:00
updated: 2015-11-01 08:21:35
tags: 
- ubuntu
- r
categories: 
- linux

---
今天DST里面有个插件作者问我关于Replace INTO和INSERT INTO的区别,我和他说晚上上我的blog看吧,那时候还在忙,现在从MYSQL手册里找了点东西,MYSQL手册里说REPLACE INTO说的还是比较详细的.

REPLACE的运行与INSERT很相像。只有一点除外，如果表中的一个旧记录与一个用于PRIMARY KEY或一个UNIQUE索引的新记录具有相同的值，则在新记录被插入之前，旧记录被删除。请参见13.2.4节，“INSERT语法”。

注意，除非表有一个PRIMARY KEY或UNIQUE索引，否则，使用一个REPLACE语句没有意义。该语句会与INSERT相同，因为没有索引被用于确定是否新行复制了其它的行。

所有列的值均取自在REPLACE语句中被指定的值。所有缺失的列被设置为各自的默认值，这和INSERT一样。您不能从当前行中引用值，也不能在新行中使用值。如果您使用一个例如“SET col_name = col_name + 1”的赋值，则对位于右侧的列名称的引用会被作为DEFAULT(col_name)处理。因此，该赋值相当于SET col_name = DEFAULT(col_name) + 1。

为了能够使用REPLACE，您必须同时拥有表的INSERT和DELETE权限。


<!--more-->


REPLACE语句会返回一个数，来指示受影响的行的数目。该数是被删除和被插入的行数的和。如果对于一个单行REPLACE该数为1，则一行被插入，同时没有行被删除。如果该数大于1，则在新行被插入前，有一个或多个旧行被删除。如果表包含多个唯一索引，并且新行复制了在不同的唯一索引中的不同旧行的值，则有可能是一个单一行替换了多个旧行。

受影响的行数可以容易地确定是否REPLACE只添加了一行，或者是否REPLACE也替换了其它行：检查该数是否为1（添加）或更大（替换）。

如果您正在使用C API，则可以使用mysql_affected_rows()函数获得受影响的行数。

目前，您不能在一个子查询中，向一个表中更换，同时从同一个表中选择。

以下是所用算法的更详细的说明（该算法也用于LOAD DATA...REPLACE）：

1. 尝试把新行插入到表中

2. 当因为对于主键或唯一关键字出现重复关键字错误而造成插入失败时：

3. 从表中删除含有重复关键字值的冲突行

4. 再次尝试把新行插入到表中

今天喉咙疼死我了,闪人睡觉去.

使用格式:

    REPLACE [LOW_PRIORITY | DELAYED]
    [INTO] tbl_name [(col_name,...)]
    VALUES ({expr | DEFAULT},...),(...),...
    或：

    REPLACE [LOW_PRIORITY | DELAYED]
    [INTO] tbl_name
    SET col_name={expr | DEFAULT}, ...
    或：

    REPLACE [LOW_PRIORITY | DELAYED]
    [INTO] tbl_name [(col_name,...)]
    SELECT ...

