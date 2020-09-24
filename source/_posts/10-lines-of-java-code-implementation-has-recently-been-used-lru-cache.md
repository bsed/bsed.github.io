---
title: 10行Java代码实现最近被使用（LRU）缓存[转]
date: 2015-08-12 10:25:00
updated: 2015-08-12 15:16:29
tags: 
- mysql
- php
- utf8mb4
categories: 
- sql

---
![LRU-Cache-650x296.png][1]
在最近的面试中，我曾被多次问到，怎么实现一个最近最少使用（LRU）的缓存。缓存可以通过哈希表来实现，然而为这个缓存增加大小限制会变成另一个有意思的问题。现在我们看一下怎么实现。

最近最少使用缓存的回收

为了实现缓存回收，我们需要很容易做到：

查询出最近最晚使用的项
给最近使用的项做一个标记
链表可以实现这两个操作。检测最近最少使用的项只需要返回链表的尾部。标记一项为最近使用的项只需要从当前位置移除，然后将该项放置到头部。比较困难的事情是怎么快速的在链表中找到该项。

哈希表的帮助

看一下我们工具箱中的数据结构，哈希表可以在（消耗）常量的时间内索引到某个对象。如果我们创建一个形如key->链表节点的哈希表，我们就能够在常量时间内找到最近使用的节点。更甚的是，我们也能够在常量时间内判断节点的是否存在（或不存在）；

找到这个节点后，我们就能将这个节点移动到链表的最前端，标记为最近使用的项了。

Java的捷径

据我所知，很少有一种编程语言的标准库中有通用的数据结构能提供上述功能的。这是一种混合的数据结构，我们需要在哈希表的基础上建立一个链表。但是Java已经为我们提供了这种形式的数据结构-LinkedHashMap！它甚至提供可覆盖回收策略的方法（见removeEldestEntry[文档](http://docs.oracle.com/javase/7/docs/api/java/util/LinkedHashMap.html#removeEldestEntry(java.util.Map.Entry))）。唯一需要我们注意的事情是，改链表的顺序是插入的顺序，而不是访问的顺序。但是，有一个构造函数提供了一个选项，可以使用访问的顺序（见[文档](http://docs.oracle.com/javase/7/docs/api/java/util/LinkedHashMap.html#LinkedHashMap(int,%20float,%20boolean))）。


<!--more-->


无需多说：

    import java.util.LinkedHashMap;
    import java.util.Map;
     
    public LRUCache<K, V> extends LinkedHashMap<K, V> {
      private int cacheSize;
     
      public LRUCache(int cacheSize) {
        super(16, 0.75, true);
        this.cacheSize = cacheSize;
      }
     
      protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() >= cacheSize;
      }
    }

原文链接： [Chriswu](http://chriswu.me/blog/a-lru-cache-in-10-lines-of-java/) 翻译： [ImportNew.com - paddx](http://www.importnew.com/16264.html)


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/2922126952.png