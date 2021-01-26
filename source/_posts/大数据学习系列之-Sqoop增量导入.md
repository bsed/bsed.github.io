---
title: "大数据学习系列之 Sqoop增量导入"
categories: [ "大数据/NLP" ]
tags: [ "hadoop","sqoop" ]
draft: false
slug: "sqoop-incremental-import"
date: "2016-12-01 12:27:00"
---

## 核心参数

 - `–check-column`，用来指定一些列，这些列在导入时用来检查做决定数据是否要被作为增量数据，在一般关系型数据库中，都存在类似`Last_Mod_Date`的字段或主键。注意：这些被检查的列的类型不能是任意字符类型，例如Char，VARCHAR…（即字符类型不能作为增量标识字段）
 - `–incremental`，用来指定增量导入的模式（Mode），`append`和`lastmodified`
 - `–last-value`，指定上一次导入中检查列指定字段最大值

## 增量模式（Mode）

 - 1、append，在导入的新数据ID值是连续时采用，对数据进行附加
 - 2、lastmodified，在源表中有数据更新的时候使用，检查列就必须是一个时间戳或日期类型的字段，更新完之后，last-value会被设置为执行增量导入时的当前系统时间


<!--more-->


### Append

加不加`–last-value`的区别在于：数据是否冗余，如果不加，则会导入源表中的所有数据导致数据冗余。

### Lastmodified

当使用`–incremental` lastmodified模式进行导入且导入目录已存在时，需要使用`–merge-key`或`–append` 
导入>=last-value的值。

### 示例
```bash
sqoop import --connect jdbc:mysql://bigdata:3306/sqooptest --table bigdata --username root --password 123456 --check-column last_mod_ts --incremental lastmodified --last-value "2016-11-11 10:32:22" --merge-key class_id -m 1
```

参考：

 - https://ask.hellobi.com/blog/marsj/5130