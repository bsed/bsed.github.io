---
title: "如何使用 explain 来分析一条 sql 新能"
categories: [ "SQL" ]
tags: [ "explain" ]
draft: false
slug: "how-to-use-explain-to-analyze-the-performance-of-an-sql"
date: "2019-06-17 10:59:00"
---

网上其实已经有非常多的文章都很详细的介绍了 explain 的使用，这篇文章将实例和原理结合起来，尽量让你有更好的理解，相信我，认真看完你应该会有特别的收获。

explain 翻译过来就是解释的意思， 在 mysql 里被称作**执行计划**，即可以通过该命令看出 mysql 在经过优化器分析后决定要如何执行该条 sql 。

说到优化器，再多说一句，mysql 内置了一个强大的优化器，优化器的主要任务就是把你写的 sql 再给优化一下，尽可能以更低成本去执行，比如扫描更少的行数，避免排序等。


<!--more-->


你可能会问，一般在什么时候会要用 explain 呢，大多数情况下都是从 mysql 的**慢查询日志**中揪出来一些查询效率比较慢的 sql 来使用 explain 分析，也有的是就是在对 mysql 进行优化的时候，比如添加索引，通过 explain 来分析添加的索引能否被命中，还有的就是在业务开发的时候，在满足需求的情况下，你可能需要通过 explain 来选择一个更高效的 sql。

那么 explain 该怎么用呢，很简单，直接在 sql 前面加上 explain 就行了，如下所示。

```mysql
mysql> explain select * from t_explain;
+----+-------------+-----------+------+---------------+------+---------+------+--------+-------+
| id | select_type | table     | type | possible_keys | key  | key_len | ref  | rows   | Extra |
+----+-------------+-----------+------+---------------+------+---------+------+--------+-------+
|  1 | SIMPLE      | t_explain | ALL  | NULL          | NULL | NULL    | NULL | 126328 |       |
+----+-------------+-----------+------+---------------+------+---------+------+--------+-------+
1 row in set (0.00 sec)
```

可以看到，explain 会返回约 10 个字段，不同版本返回的字段有些许差异，每个字段都代表着具体的意义，这篇文章我不打算把每个字段都详细的介绍一遍，东西比较多，怕你也不容易记住，不如先把几个重要的字段好好理解了。

其中 type、key、rows、Extra 这几个字段我认为是比较重要的，我们接下来通过具体的实例来帮你更好的理解这几个字段的含义。

首先有必要简单介绍下这几个字段的字面意思。

type 表示 mysql 访问数据的方式，常见的有全表扫描（all）、遍历索引（index）、区间查询（range）、常量或等值查询（ref、eq_ref）、主键等值查询（const）、当表中只有一条记录时（system）。下面是效率从最好到最差的一个排序。

```
system > const > eq_ref > ref > range > index > all
```

key 表示查询过程实际会用到的索引名称。
rows 表示查询过程中可能需要扫描的行数，这个数据不一定准确，是mysql 抽样统计的一个数据。
Extra 表示一些额外的信息，通常会显示是否使用了索引，是否需要排序，是否会用到临时表等。
好了，接下来正式开始实例分析。

还是沿用前面文章中创建的存储引擎创建一个测试表，我们这里插入 10 w 条测试数据，表结构如下：

```mysql
CREATE TABLE `t_explain` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `a` int(11) DEFAULT NULL,
  `b` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=84938937 DEFAULT CHARSET=utf8;
```
然后看下面这条查询语句，注意这个表目前只有一个主键索引，还没有创建普通索引。

```mysql
mysql> explain select * from t_explain;
+----+-------------+-----------+------+---------------+------+---------+------+--------+-------+
| id | select_type | table     | type | possible_keys | key  | key_len | ref  | rows   | Extra |
+----+-------------+-----------+------+---------------+------+---------+------+--------+-------+
|  1 | SIMPLE      | t_explain | ALL  | NULL          | NULL | NULL    | NULL | 126328 |       |
+----+-------------+-----------+------+---------------+------+---------+------+--------+-------+
1 row in set (0.00 sec)
```

其中 type 值为 ALL，表示全表扫描了，大家注意看到 rows 这个字段显示有 100332 条，实际上我们一共才 10w 条数据，所以这个字段只是 mysql 的一个预估，并不一定准确。这种全表扫描的效率非常低，是需要重点被优化的。

接下来我们分别给字段 a 和 b 添加普通索引，然后再看下添加索引后的几条 sql 。

```mysql
mysql> alter table t_explain add index index_a(a);
Query OK, 0 rows affected (0.41 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> alter table t_explain add index index_b(b);
Query OK, 0 rows affected (0.41 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> show index from t_explain;
+-----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table     | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| t_explain |          0 | PRIMARY  |            1 | id          | A         |      126067 |     NULL | NULL   |      | BTREE      |         |               |
| t_explain |          1 | index_a  |            1 | a           | A         |         200 |     NULL | NULL   | YES  | BTREE      |         |               |
| t_explain |          1 | index_b  |            1 | b           | A         |         200 |     NULL | NULL   | YES  | BTREE      |         |               |
+-----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
3 rows in set (0.00 sec)
```

```mysql
mysql> explain select * from t_explain where a > 1000;
+----+-------------+-----------+------+---------------+------+---------+------+--------+-------------+
| id | select_type | table     | type | possible_keys | key  | key_len | ref  | rows   | Extra       |
+----+-------------+-----------+------+---------------+------+---------+------+--------+-------------+
|  1 | SIMPLE      | t_explain | ALL  | index_a       | NULL | NULL    | NULL | 126067 | Using where |
+----+-------------+-----------+------+---------------+------+---------+------+--------+-------------+
1 row in set (0.00 sec)
```

上面这条 sql 看起来是不是有点疑惑呢，type 竟然显示`ALL`，刚刚不是给字段 a 添加索引了么，而且 possible_keys 也显示了有 `a_index` 可用，但是 key 显示 `null`，表示 mysql 实际上并不会使用 a 索引，这是为啥？

这里是因为 `select *` 的话还需要回到*主键索引上查找 b 字段*，这个过程叫**回表**，这条语句会筛选出 9w 条满足条件的数据，也就是说这 9w 条数据都需要回表操作，全表扫描都才 10w 条数据，所以在 mysql 的优化器看来还不如直接全表扫描得了，至少还免去了回表过程了。

当然也不是说只要有回表操作就不会命中索引，用不用索引关键还在于 mysql 认为哪种查询代价更低，我们把上面的 sql 中 where 条件再稍微改造一下。

```mysql
mysql> explain select * from t_explain where a > 99000;
+----+-------------+-----------+-------+---------------+---------+---------+------+------+-------------+
| id | select_type | table     | type  | possible_keys | key     | key_len | ref  | rows | Extra       |
+----+-------------+-----------+-------+---------------+---------+---------+------+------+-------------+
|  1 | SIMPLE      | t_explain | range | index_a       | index_a | 5       | NULL | 1307 | Using where |
+----+-------------+-----------+-------+---------------+---------+---------+------+------+-------------+
1 row in set (0.00 sec)
```

这回 type 值为` range` 了，key 为 `index_a` ，表示**命中了 a 索引**，是一个不错的选择，是因为满足这条 sql 条件的只有 1000 条数据，mysql 认为 1000 条数据就算回表也要比全表扫描的代价低，所以说 mysql 其实是个很聪明的家伙。

我们还可以看到 Extra 字段中值为 Using index condition，这个意思是指用到了索引，但是需要回表，再看下面这个语句。

```mysql
mysql> explain select a from t_explain where a > 99000;
+----+-------------+-----------+-------+---------------+---------+---------+------+------+--------------------------+
| id | select_type | table     | type  | possible_keys | key     | key_len | ref  | rows | Extra                    |
+----+-------------+-----------+-------+---------------+---------+---------+------+------+--------------------------+
|  1 | SIMPLE      | t_explain | range | index_a       | index_a | 5       | NULL | 1307 | Using where; Using index |
+----+-------------+-----------+-------+---------------+---------+---------+------+------+--------------------------+
1 row in set (0.00 sec)
```
这个 Extra 中的值为 `Using where; Using index` ，表示**查询用到了索引，且要查询的字段在索引中就能拿到，不需要回表**，显然这种效率比上面的要高，所以不要轻易写 select * ，只查询业务需要的字段即可，这样可以尽可能避免回表。

再来看一个需要排序的。

```mysql
mysql> explain select a from t_explain where a > 99000 order by b;
+----+-------------+-----------+-------+---------------+---------+---------+------+------+-----------------------------+
| id | select_type | table     | type  | possible_keys | key     | key_len | ref  | rows | Extra                       |
+----+-------------+-----------+-------+---------------+---------+---------+------+------+-----------------------------+
|  1 | SIMPLE      | t_explain | range | index_a       | index_a | 5       | NULL | 1307 | Using where; Using filesort |
+----+-------------+-----------+-------+---------------+---------+---------+------+------+-----------------------------+
1 row in set (0.00 sec)
```

这个 Extra 中返回了一个 `Using filesort`，意味着需要排序，这种是需要重点优化的，也就是说查到数据后，还需要 mysql 在内存中对其进行排序，你要知道索引本身就是有序的，所以一般来讲要尽量利用索引的有序性，比如像下面这样写。

```mysql
mysql> explain select a from t_explain where a > 99990 order by a;
+----+-------------+-----------+-------+---------------+---------+---------+------+------+--------------------------+
| id | select_type | table     | type  | possible_keys | key     | key_len | ref  | rows | Extra                    |
+----+-------------+-----------+-------+---------------+---------+---------+------+------+--------------------------+
|  1 | SIMPLE      | t_explain | range | index_a       | index_a | 5       | NULL |   10 | Using where; Using index |
+----+-------------+-----------+-------+---------------+---------+---------+------+------+--------------------------+
1 row in set (0.00 sec)
```

我们再创建一个复合索引看看。

```mysql
mysql> alter table t_explain add index index_ab(a,b);
Query OK, 0 rows affected (0.44 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

```mysql
mysql> explain select * from t_explain where a > 1000;
+----+-------------+-----------+-------+------------------+----------+---------+------+-------+--------------------------+
| id | select_type | table     | type  | possible_keys    | key      | key_len | ref  | rows  | Extra                    |
+----+-------------+-----------+-------+------------------+----------+---------+------+-------+--------------------------+
|  1 | SIMPLE      | t_explain | range | index_a,index_ab | index_ab | 5       | NULL | 63033 | Using where; Using index |
+----+-------------+-----------+-------+------------------+----------+---------+------+-------+--------------------------+
1 row in set (0.00 sec)
```

这条 sql 刚刚在上面也有讲到过，在没有创建复合索引的时候，是**走的全表扫描**，现在其实是利用了**覆盖索引**，同样是免去了回表过程，即在  `index_ab` 索引上就能找出要查询的字段。

这篇文章通过几个实例介绍了如何使用 *explain 分析一条 sql 的执行计划*，也提到了一些常见的索引优化，事实上还有更多的可能性，你也可以自己去写一个 sql ，然后使用 explain 分析，看看有哪些是可以被优化的。

下面给出测试数据：
创建存储过程：
```
CREATE DEFINER=`root`@`localhost` PROCEDURE `fenxi`.`insert_t_explain`(IN n INT)
begin
    DECLARE i INT DEFAULT 1;
    DECLARE a INT DEFAULT 0;
    DECLARE b TINYINT DEFAULT 1;
    WHILE i < n DO
        SET a = FLOOR(0 + RAND()*100000);
        SET b = FLOOR(1 + RAND()*2);
        INSERT INTO `t_explain`(a,b) VALUES (a, b);
        SET i = i + 1;
    END WHILE;
    end
```
如图：
![59004-t2sx63zz4ai.png](https://imgs.gnux.cn/usr/uploads/2019/06/3432218594.png)
调用存储过程
```
call insert_t_explain(100000);
```
