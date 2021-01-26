---
title: " 解决MySQL出现大量unauthenticated use"
categories: [ "SQL" ]
tags: [ "mysql","unauthenticated use" ]
draft: false
slug: "mysql-appear-to-solve-a-large-number-of-use-unauthenticated"
date: "2010-11-01 08:04:00"
---

在MySQL中执行Show Processlist语句可以看到发现大量unauthenticated user进程卡住，在网上搜索一翻，找到问题根源：

发现这属于官方一个系统上的特殊设定，亦可称呼他为MySQL的bug，不管连结的的方式是经过hosts或是IP的模式，他都会对DNS做反查。MySQL会尝试去反查IP->DNS，由于反查解析过慢，无法应付快速多量的查询。


<!--more-->


解决方式很简单：启动MySQL的时候，添加--skip-name-resolve选项，禁止域名解析，问题解决。

是为记录！