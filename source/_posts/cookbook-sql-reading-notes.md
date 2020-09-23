---
title: SQL COOKBOOK阅读笔记
date: 2014-01-27 23:12:00
updated: 2015-10-19 21:24:05
tags: 
- zombie
categories: 
- linux

---
## 1. 尽量不要使用select *

返回表中的所有field时，使用`select *`确实很方便，但是不推荐这么做，应该在select后将所有的field都列出来：一是方便自己，清楚地知道返回了哪些field；而是方便他人，在看你的代码时，别人不一定知道表中有哪些列。
这两种方式的性能是一样的。

## 2. 尽量使用as创建易读的别名

### 2.1 尽量使用易读的别名

    mysql> select sal as salary, empName as employName from employ;

> 使用易读的别名，可以让你的sql语句更容易被理解。

### 2.2 在where中使用别名：

    mysql> select sal as salary from employ where salary >= 10000;


<!--more-->


> 该语句报错，因为salary字段未识别，可以将该语句包装成一个临时表：

    mysql> select * from (select sal as salary from employ) as t where salary >= 10000;

> 为什么：因为where语句在select之前执行，所以在第一条语句中，salary还没有创建；而from语句在where语句之前执行，所以在第二条语句中，临时表t中有字段salary。


## 3. 使用concat函数进行字段值的连接

将字段值和字符串拼接起来，使用`concat()`函数：

    mysql> select concat(startIp, " : ", endIp) as ipSeg from ip_data limit 1;
	+---------------------+
	| ipSeg               |
	+---------------------+
	| 17563648 : 17825791 | 
	+---------------------+

## 4. 使用case when进行条件判断

在select语句中，可以对返回结果进行逻辑处理。使用case表达式，语法格式如下。else可以省略，如果所有的when
都不匹配，默认返回null；

	case
		when ... then
		when ... then
		else ...
	end

> 注意，还有一个case表达式，用于存储过程，以`end case`结尾。

    mysql> select netId, areaId, case when netId > 10 then 'big' when netId < 5 then 'small' else 'normal' end as status from ip_data limit 5;
	+-------+--------+--------+
	| netId | areaId | status |
	+-------+--------+--------+
	|    11 |   4019 | big    | 
	|     2 |   2813 | small  | 
	|    10 |     35 | normal | 
	|     1 |   2110 | small  | 
	|    10 |     35 | normal | 
	+-------+--------+--------+

## 5. 使用rand返回随机的记录

可以使用`rand()`函数返回一个随机数，通过order by排序即可。rand()对每一条记录返回0.0到1.0之间的浮点数。
当然也可以在order by之后跟一个常量浮点数，但此时结果是固定的。

    mysql> select ip_data.netId, ip_data.areaId from ip_data order by rand() limit 5;
	+-------+--------+
	| netId | areaId |
	+-------+--------+
	|     7 |   4019 | 
	|    10 |   1806 | 
	|     2 |   4608 | 
	|    13 |     42 | 
	|     3 |   4502 | 
	+-------+--------+

## 6. 使用is null判断字段是否为null

判断字段null值，不能使用=或者!=，即使null和null也不能通过=比较。应该使用 `is null`, 或者 `is not null`。
    
    mysql> select startIp, areaId from ip_data where netId  is null;
    
## 7. 使用coalesce函数对字段进行默认处理

对字段的返回值进行合并，使用函数`coalesce(field, default)`：如果field不为null，取field的值，如果field值为null，取设置的默认值default。

	mysql> select number, coalesce(number, 1000) as new_number  from students;
	+--------+------------+
	| number | new_number |
	+--------+------------+
	|    111 |        111 | 
	|   NULL |       1000 | 
	|    333 |        333 | 
	|  12345 |      12345 | 
	|   NULL |       1000 | 
	|    777 |        777 | 
	|   NULL |       1000 | 
	+--------+------------+

> 当然，在select里使用`case when end`也能达到同样的效果，但显然使用coalesce()更简洁。

## 8. 在order by之后通过数字指定字段进行排序

order by一般后跟字段名，表示根据字段名排序；也可以跟一个整数，该整数的值与select中的字段相对应，从1开始，表示根据select中的第几个字段排序，因此，该整数不能大于select中的字段个数。如下两种情况是等价的：

    mysql> select id, startIp, endIp, netId from ip_data order by id asc limit 5; 
    mysql> select id, startIp, endIp, netId from ip_data order by 1 asc limit 5;
	+----+----------+----------+-------+
	| id | startIp  | endIp    | netId |
	+----+----------+----------+-------+
	|  1 | 17563648 | 17825791 |    11 | 
	|  2 | 18350080 | 18874367 |     2 | 
	|  3 | 19726336 | 19791871 |    10 | 
	|  4 | 19922944 | 20054015 |     1 | 
	|  5 | 20054016 | 20119551 |    10 | 
	+----+----------+----------+-------+

另外，可以根据多个字段排序，优先级从左到右，即先根据第一个字段排序，如果第一个字段的值相等，然后根据第二个字段的值排序，依此类推：

    >> select id, startIp, endIp, netId from ip_data order by netId asc, endIp desc limit 5;
	+-------+------------+------------+-------+
	| id    | startIp    | endIp      | netId |
	+-------+------------+------------+-------+
	| 17351 | 3757047808 | 3757572095 |     1 | 
	| 17348 | 3755737088 | 3755868159 |     1 | 
	| 17347 | 3755343872 | 3755474943 |     1 | 
	| 17341 | 3754295296 | 3754426367 |     1 | 
	| 17337 | 3750756352 | 3751804927 |     1 | 
	+-------+------------+------------+-------+

## 9. 通过substring函数对字段子串排序

使用函数`substring()`可以获取字符串的子串，使用`order by`可以根据该子串进行排序。
注意：substring(str, pos)表示从pos开始的子串，其中位移从1开始，所以如果要表示
取字符串的最后两个字符，应该是substring(str, length(str)-1).

	mysql> select file_path, file_name from file order by substr(file_name, length(file_name)-1) desc  limit 5;
	+----------------+-----------------------+
	| file_path      | file_name             |
	+----------------+-----------------------+
	| /opt/rankFile/ | phb_variety_day_issue | 
	| /opt/rankFile/ | catalog_variety       | 
	| /opt/rankFile/ | catalog_tv            | 
	| /opt/rankFile/ | catalog_cartoon       | 
	| /opt/rankFile/ | catalog_doc           | 
	+----------------+-----------------------+

## 10. 如何对含有null值的字段进行排序

mysql中字段的null值在排序时默认时按照最小值排序的，即升序时，null值在最前面，降序时在最后面。通过在select中使用`case when`对null值进行判断，然后定义排序顺序。
如升序时将null值放在最后：

	mysql> select name, number from (select name, number, case when number is null then 0 else 1 end as is_null from students) as t order by is_null desc, number;
	+----------+--------+
	| name     | number |
	+----------+--------+
	| lingguo  |    111 | 
	| lisi     |    333 | 
	| guanyu   |    777 | 
	| wangwu   |  12345 | 
	| zhangsan |   NULL | 
	| zhangfei |   NULL | 
	| lingguo  |   NULL | 
	+----------+--------+
    
## 11. 字段值不同，排序的字段不同，如何实现？

`case when`语句也可以在`order by`中，不同的条件，使用不同的字段进行排序。
比如：如果number字段的值不为null，根据number排序，否则，根据enable排序：

	mysql> select name, number, enable from students order by case when number is null then enable else number end;
	+----------+--------+--------+
	| name     | number | enable |
	+----------+--------+--------+
	| zhangsan |   NULL |      0 | 
	| zhangfei |   NULL |      0 | 
	| lingguo  |   NULL |      1 | 
	| lingguo  |    111 |      1 | 
	| lisi     |    333 |      1 | 
	| guanyu   |    777 |      0 | 
	| wangwu   |  12345 |      0 | 
	+----------+--------+--------+

### 参考：

+ [SQL Cookbook](http://book.douban.com/subject/1840666/)
