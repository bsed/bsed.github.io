---
title: "GROUP by 操作的优化"
categories: [ "SQL" ]
tags: [ "mysql","optimize","groupby" ]
draft: false
slug: "optimization-of-by-group-operation"
date: "2010-06-13 23:00:00"
---

默认情况下， MySQL 在执行 GROUP BY col1 ， col2…. 操作的时候，会按照 GROUP BY 字段的顺序进行排序。如果显式包括一个包含相同的列的 ORDER BY 子句，则对 MySQL 的实际执行性能没有什么额外的影响。

如果查询包括 GROUP BY 操作， 但是不需要对结果进行排序，或者对默认的排序结果不满意，希望获得结果后再由程序进一步处理的时候，可以指定 ORDER BY NULL 禁止排序，从而避免排序结果的消耗。

下面介绍的例子对比了开启 / 关闭 GROUP BY 排序的执行计划：


<!--more-->


```
mysql> desc select dep,pos,avg(sal) from employee group by dep,pos \G
*********\* 1. row **********
id: 1
select_type: SIMPLE
table: employee
type: ALL
possible_keys: NULL
key: NULL
key_len: NULL
ref: NULL
rows: 10
Extra: Using temporary; Using filesort
1 row in set (0.00 sec)
```

```
mysql> desc select dep,pos,avg(sal) from employee group by dep,pos order by null \G
*********\* 1. row **********
id: 1
select_type: SIMPLE
table: employee
type: ALL
possible_keys: NULL
key: NULL
key_len: NULL
ref: NULL
rows: 10
Extra: Using temporary
1 row in set (0.00 sec)
```
从执行计划可以看到，使用了 `ORDER BY NULL` 的 SQL 减少了文件排序的步骤，当返回结果集很大时，对于 `GROUP BY` 的性能是有很大改善的。

本文来源：[http://www.unix-center.net](http://www.unix-center.net)
