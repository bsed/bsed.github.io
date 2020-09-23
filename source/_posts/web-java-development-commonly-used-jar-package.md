---
title:  Java Web开发常用Jar包
date: 2011-04-22 22:21:00
updated: 2015-09-27 21:12:52
tags: 
- emmet
categories: 
- css

---
## Json处理

**FastJson**是阿里巴巴的Json处理工具包，包括序列化和反序列化两部分。FastJson具有极快的性能，超越任何其它Java Json包。[下载地址](http://repo1.maven.org/maven2/com/alibaba/fastjson/)
**Jackson**是Java处理JSON格式数据的工具包，在FastJson出现以前，其JSON处理性能是最好的，同时也是Spring MVC中内置的JSON解析方式。Jackson需要三个包jackson-annotations、jackson-core、jackson-databind。[下载地址](http://repo1.maven.org/maven2/com/fasterxml/jackson/core/)

## XML解析

**Dom4j**是开源的XML解析解析包，具有性能优异、功能强大和极端易用使用的特点。


<!--more-->


## 数据库连接池

**Druid**是阿里巴巴提供的一个高效、功能强大、可扩展性好的数据库连接池。[下载地址](http://central.maven.org/maven2/com/alibaba/druid/)
`DBCP（Database Connection Pool）`是一个依赖`Apache Commons Pool`对象池机制的数据库连接池，Tomcat的数据源使用的就是DBCP。
**C3P0**是一个开放源代码的JDBC连接池，Hibernate的发行包中默认使用此连接池。