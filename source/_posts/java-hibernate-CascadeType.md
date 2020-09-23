---
title: hibernate CascadeType属性说明
date: 2010-12-01 08:51:00
updated: 2014-11-08 15:26:20
tags: 
- golang
- golibs
categories: 
- go

---
CascadeType.PERSIST只有A类新增时，会级联B对象新增。若B对象在数据库存（跟新）在则抛异常（让B变为持久态） 


<!--more-->


CascadeType.MERGE指A类新增或者变化，会级联B对象（新增或者变化） 

CascadeType.REMOVE只有A类删除时，会级联删除B类； 

CascadeType.ALL包含所有

CascadeType.REFRESH没用过。 

综上：大多数情况用CascadeType.MERGE就能达到级联跟新又不报错，用CascadeType.ALL时要斟酌下CascadeType.REMOVE 