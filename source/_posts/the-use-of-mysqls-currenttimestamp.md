---
title: MySQL 的CURRENT_TIMESTAMP使用
date: 2018-03-20 11:36:00
updated: 2018-03-20 13:37:40
tags: 
- linux
- EGL
categories: 
- linux

---
MySQL的日期类型可以使用`CURRENT_TIMESTAMP来`指定默认值，但是这个跟MySQL的版本及日期的具体类型有关，只有**5.6之后**的版本才能使用`CURRENT_TIMESTAMP作为DATETIME`的默认值。 
例如：
```mysql
ALTER TABLE t_user ADD update_time DATETIME DEFAULT CURRENT_TIMESTAMP
```


<!--more-->


在5.6之前的版本，使用CURRENT_TIMESTAMP作为默认值时，就会出现下面的错误
```bash
[Err] 1067 - Invalid default value for 'RcTime'
```
配置如图：
![08786-8x2d6halg2j.png](https://imgs.gnux.cn/usr/uploads/2018/03/3638600301.png)