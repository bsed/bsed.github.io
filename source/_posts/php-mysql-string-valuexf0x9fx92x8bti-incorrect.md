---
title: php 解决MySQL插入数据出现 Incorrect string value: '\xF0\x9F\x92\x8BTi...'错误
date: 2014-08-19 09:49:00
updated: 2015-08-19 09:52:03
tags: 
- css
categories: 
- css

---
> 在项目中向 MySQL
> 插入数据时，发现数据插入不完整，通过调试，发现插入语句也没什么特殊的错误。但是就是差不进去，于是就打开mysqli错误的调试

    $ret = mysqli_query($this->conn, $sql) or die(mysqli_error($this->conn));

结果弹出如下错误信息：

    Incorrect string value: '\xF0\x9F\x92\x8BTi...'

有错误信息就好办了，结果上网一查结果是：mysql编码格式utf-8格式，不支持带四字节的字符串插入。

### 网上提供两种方法：

1.升级 MySQL ，然后将对应的数据类型改为utf8mb4类型

2.出现的四字节UTF-8字符过滤或转化为自定义类型


<!--more-->


由于装的MySQL版本为5.1，所以方法1不适合；本人选了第二种过滤字符串，变成合适的3字节utf-8

    $str = preg_replace('/[\x{10000}-\x{10FFFF}]/u', '', $str);

虽然还是不能很完美解决,但是至少保证每条数据都可以正确插入！

如果想完美解决的话，最好升级MySql，直接改数据类型为utf8mb4类型

原文：[http://blog.csdn.net/qivan/article/details/37691813?utm_source=tuicool](http://blog.csdn.net/qivan/article/details/37691813?utm_source=tuicool)