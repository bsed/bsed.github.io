---
title: MySQL联合查询
date: 2008-11-26 08:08:00
updated: 2015-11-01 08:18:24
tags: 
- g-ithub
- octopress
categories: 
- linux

---
首先我们假设有2个表A和B，他们的表结构和字段分别为：

*表A：*
| ID | Name |
| -- |  -- |
| 1 | Tim | 
| 2 | Jimmy | 
| 3 | John | 
| 4 | Tom | 

*表B：*
| ID | Hobby |
| --- | --- |
| 1 | Football |
| 2 | Basketball |
| 2 | Tennis |
| 4 | Soccer |


<!--more-->


## 1．  内联结：
`Select A.Name, B.Hobby from A, B where A.id = B.id`，这是隐式的内联结，查询的结果是：
| Name | Hobby |
| -- | -- |
| Tim | Football |
| Jimmy | Basketball |
| Jimmy | Tennis |
| Tom | Soccer  |

它的作用和 `Select A.Name from A INNER JOIN B ON A.id = B.id` 是一样的。这里的INNER JOIN换成CROSS JOIN也是可以的。

## 2．  外左联结

`Select A.Name from A Left JOIN B ON A.id = B.id`，典型的外左联结，这样查询得到的结果将会是保留所有A表中联结字段的记录，若无与其相对应的B表中的字段记录则留空，结果如下：
| Name | Hobby |
| --- | -- |
| Tim |Football |
| Jimmy | Basketball，Tennis |
| John |  |  	
| Tom | Soccer |

所以从上面结果看出，因为A表中的John记录的ID没有在B表中有对应ID，因此为空，但Name栏仍有John记录。
## 3．  外右联结

如果把上面查询改成外右联结：`Select A.Name from A Right JOIN B ON A.id = B.id`，则结果将会是：
| Name | Hobby |
| --- |  --- |
| Tim | Football |
| Jimmy | Basketball |
| Jimmy | Tennis |
| Tom | Soccer |

这样的结果都是我们可以从外左联结的结果中猜到的了。
说到这里大家是否对联结查询了解多了？这个原本看来高深的概念一下子就理解了，恍然大悟了吧（呵呵，开玩笑了）？最后给大家**讲讲MySQL联结查询中的某些参数的作用：**

1．USING (column_list)：其作用是为了方便书写联结的多对应关系，大部分情况下USING语句可以用ON语句来代替，如下面例子：

`a LEFT JOIN b USING (c1,c2,c3)`，其作用相当于下面语句
`a LEFT JOIN b ON a.c1=b.c1 AND a.c2=b.c2 AND a.c3=b.c3`

只是用ON来代替会书写比较麻烦而已。

2．NATURAL [LEFT] JOIN：这个句子的作用相当于INNER JOIN，或者是在USING子句中包含了联结的表中所有字段的Left JOIN（左联结）。

3．STRAIGHT_JOIN：由于默认情况下MySQL在进行表的联结的时候会先读入左表，当使用了这个参数后MySQL将会先读入右表，这是个MySQL的内置优化参数，大家应该在特定情况下使用，譬如已经确认右表中的记录数量少，在筛选后能大大提高查询速度。

最后要说的就是，在MySQL5.0以后，运算顺序得到了重视，所以对多表的联结查询可能会错误以子联结查询的方式进行。譬如你需要进行多表联结，因此你输入了下面的联结查询：

    SELECT t1.id,t2.id,t3.id
    FROM t1,t2
    LEFT JOIN t3 ON (t3.id=t1.id)
    WHERE t1.id=t2.id;

但是MySQL并不是这样执行的，其后台的真正执行方式是下面的语句：

    SELECT t1.id,t2.id,t3.id
    FROM t1,(  t2 LEFT JOIN t3 ON (t3.id=t1.id)  )
    WHERE t1.id=t2.id;

这并不是我们想要的效果，所以我们需要这样输入：

    SELECT t1.id,t2.id,t3.id
    FROM (t1,t2)
    LEFT JOIN t3 ON (t3.id=t1.id)
    WHERE t1.id=t2.id;

在这里括号是相当重要的，因此以后在写这样的查询的时候我们不要忘记了多写几个括号，至少这样能避免很多错误（因为这样的错误是很难被开发人员发现的）。如果对上面内容有疑问可以来信查询：陈朋奕 chenpengyi#gmail.com，转载请注明出处及作者。
