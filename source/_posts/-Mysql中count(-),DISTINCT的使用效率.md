---
title: " Mysql中count(*),DISTINCT的使用效率"
categories: [ "SQL" ]
tags: [ "mysql","distinct" ]
draft: false
slug: "count-mysql-distinct-use-efficiency"
date: "2008-06-09 08:26:00"
---

在处理一个大数据量数据库的时候
突然发现mysql对于count(*)的不同处理会造成不同的结果

比如执行

    SELECT count(*) FROM tablename

即使对于千万级别的数据mysql也能非常迅速的返回结果
而对于

    SELECT count(*) FROM tablename WHERE.....

mysql的查询时间开始攀升

仔细查阅累下手册,发现当没有WHERE语句对于整个mysql的表进行count运算的时候
MyISAM类型的表中保存有总的行数,而当添加有WHERE限定语句的时候Mysql需要对整个表进行检索
从而得出count的数值

突然又想起来看到的不少新兴的php程序对于count的处理并没有很好的意识到这点
记录下


<!--more-->


顺便提下mysql的DISTINCT的关键字有很多你想不到的用处
1.在count 不重复的记录的时候能用到
比如`SELECT COUNT( DISTINCT id ) FROM tablename`;
就是计算talbebname表中id不同的记录有多少条

2,在需要返回记录不同的id的具体值的时候可以用
比如`SELECT DISTINCT id FROM tablename`;
返回talbebname表中不同的id的具体的值

3.上面的情况2对于需要返回mysql表中2列以上的结果时会有歧义
比如`SELECT DISTINCT id, type FROM tablename`;
实际上返回的是 id与type同时不相同的结果,也就是DISTINCT同时作用了两个字段，必须得id与tyoe都相同的才被排除了,与我们期望的结果不一样

4.这时候可以考虑使用group_concat函数来进行排除,不过这个mysql函数是在mysql4.1以上才支持的

5.其实还有另外一种解决方式,就是使用
`SELECT id, type, count(DISTINCT id) FROM tablename`
虽然这样的返回结果多了一列无用的count数据(或许你就需要这个我说的无用数据)
返回的结果是 只有id不同的所有结果和上面的4类型可以互补使用,就是看你需要什么样的数据了

**PS:**
越来越发现有很多细节是需要去探究的,
越来越发现自己写文字真的很杂乱,
有空研究下DISTINCT的效率