---
title: "java中的HashMap、Hashtable、LinkedHashMap 和TreeMap区别"
categories: [ "Java" ]
tags: [ "java","map" ]
draft: false
slug: "treemap-hashtable-linkedhashmap-and-java-in-hashmap"
date: "2011-11-13 17:36:00"
---

java为数据结构中的映射定义了一个接口java.util.Map;
它有四个实现类,分别是`HashMap`、`Hashtable`、`LinkedHashMap` 和`TreeMap`

**Map** 主要用于存储健值对，根据键得到值，因此不允许键重复,但允许值重复。

**Hashmap** 是一个最常用的Map,它根据键的HashCode值存储数据,根据键可以直接获取它的值，具有很快的访问速度。
HashMap最多只允许一条记录的键为Null;允许多条记录的值为Null;HashMap不支持线程的同步，即任一时刻可以有多个线程同时写HashMap;可能会导致数据的不一致。如果需要同步，可以用Collections的synchronizedMap方法使HashMap具有同步的能力。


<!--more-->


**Hashtable** 与 HashMap类似,不同的是:它不允许记录的键或者值为空;它支持线程的同步，即任一时刻只有一个线程能写Hashtable,因此也导致了Hashtale在写入时会比较慢。

**LinkedHashMap** 保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的.在遍历的时候会比HashMap慢。

**TreeMap** 实现SortMap接口，能够把它保存的记录根据键排序,默认是按升序排序，也可以指定排序的比较器，当用Iterator 遍历TreeMap时，得到的记录是排过序的。