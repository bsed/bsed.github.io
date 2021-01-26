---
title: "mysql select limit 大数据量查询 性能终极提升方法"
categories: [ "Java" ]
tags: [ "mysql","limit" ]
draft: false
slug: "mysql-select-limit-big-data"
date: "2014-11-22 22:17:00"
---

我们的使用mysql的时候总是想当然的使用

    select × from tables where a>0 order by id desc limit 500000,200

当我们真正使用它查询时发现这个很慢很慢


<!--more-->


我个人认为这是因为mysql并没有对该语句进行索引，即便时索引的数据，当读取前1000条时和最后一页时的速度差距时很大的原因在与这个limit

limit是一个很好的东西，但是由于他在查询的过程中io操作较大尤其是limit后面的第一个值比较大的时候

我们应该怎么做呢

我使用了一种比较新奇的方法，这个方法时在163的一片博客中写的，他当时的查询是以mssql为主的，现在查询却查询不到，我找到另一片与此文类似的 放进了引用通告中，

具体的方法就是 

    select * from tables where id >=(select id from tables where value>0 order by id desc limit 500000,1) and value>0  order by id desc limit 200

就是这种类似的语句  此语句时我按照印象来的并没有经过真正测试 理论上没有语法错误，如果有的话请各位看官简单修改之。

sql的select limit语句性能优化终极体验就在这里了。