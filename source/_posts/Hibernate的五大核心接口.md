---
title: "Hibernate的五大核心接口"
categories: [ "Java" ]
tags: [ "java","hibernate","interface" ]
draft: false
slug: "five-core-interfaces-of-hibernate"
date: "2011-06-18 12:47:00"
---

Hibernate的核心接口一共有5个，分别为:`Session`、`SessionFactory`、`Transaction`、`Query`和 `Configuration`。这5个核心接口在任何开发中都会用到。通过这些接口，不仅可以对持久化对象进行存取，还能够进行事务控制。下面对这五的核心 接口分别加以介绍。

·Session接口:
Session接口负责执行被持久化对象的CRUD操作(CRUD的任务是完成与数据库的交流，包含了很多常见的 SQL语句。)。但需要注意的是Session对象是非线程安全的。同时，Hibernate的session不同于JSP应用中的 HttpSession。这里当使用session这个术语时，其实指的是Hibernate中的session，而以后会将HttpSesion对象称 为用户session。


<!--more-->


·SessionFactory接口:
SessionFactroy接口负责初始化Hibernate。它充当数据存储源的代理，并负责创建 Session对象。这里用到了工厂模式。需要注意的是SessionFactory并不是轻量级的，因为一般情况下，一个项目通常只需要一个 SessionFactory就够，当需要操作多个数据库时，可以为每个数据库指定一个SessionFactory。

·Configuration接口:
Configuration接口负责配置并启动Hibernate，创建SessionFactory对 象。在Hibernate的启动的过程中，Configuration类的实例首先定位映射文档位置、读取配置，然后创建SessionFactory对 象。

·Transaction接口:
Transaction接口负责事务相关的操作。它是可选的，开发人员也可以设计编写自己的底层事务处理代码。
 
·Query和Criteria接口:
Query和Criteria接口负责执行各种数据库查询。它可以使用HQL语言或SQL语句两种表达方式。