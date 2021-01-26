---
title: "为什么不能用memcached存储Session"
categories: [ "PHP" ]
tags: [ "memcached","session" ]
draft: false
slug: "why-not-use-memcached-storage-session"
date: "2015-03-13 12:38:00"
---

![2015-02-24_17-25-30.jpg][1]

Memcached创建者Dormando很早就写过两篇文章[[1]](http://www.dormando.me/articles/memcached_sessions/)[[2](http://dormando.livejournal.com/495593.html)]，告诫开发人员不要用memcached存储Session。他在第一篇文章中给出的理由大致是说，如果用memcached存储Session，那么当memcached集群发生故障（比如内存溢出）或者维护（比如升级、增加或减少服务器）时，用户会无法登录，或者被踢掉线。而在第二篇文章中，他则指出，memcached的回收机制可能会导致用户无缘无故地掉线。


<!--more-->


[Titas Norkūnas](http://bearmountain.io/about-us)是DevOps咨询服务提供商[Bear Mountain](http://bearmountain.io/about-us)的联合创始人。由于看到Ruby/Rails社区忽略了Dormando那两篇文章所指出的问题，所以他近日[撰文](http://bearmountain.io/why-you-should-not-store-your-sessions-in-memcached)对此进行了进一步的阐述。他认为问题的根本在于，memcached是一个设计用于缓存数据而不是存储数据的系统，因此不应该用于存储Session。

对于Dormando的那两篇文章，他认为第一篇文章给出的原因很容易理解，而人们经常会对第二篇文章给出的原因认识不足。因此他对这个原因进行了详细地阐述：

Memcached使用“最近最少使用（LRU）”算法回收缓存。但[memcached的LRU算法针对每个slab类执行，而不是针对整体](http://work.tinou.com/2011/04/memcached-for-dummies.html)。

这意味着，如果所有Session的大小大致相同，那么它们会分成两三个slab类。所有其它大小大致相同的数据也会放入同一些slab，与Session争用存储空间。一旦slab满了，即使更大的slab中还有空间，数据也会被回收，而不是放入更大的slab中……在特定的slab中，Session最老的用户将会掉线。用户将会开始随机掉线，而最糟糕的是，你很可能甚至都不会注意到它，直至用户开始抱怨……

另外，Norkūnas提到，如果Session中增加了新数据，那么Session变大也可能会导致掉线问题出现。

有人提出将Session和其它数据分别使用单独的memcached缓存。不过，由于memcached的LRU算法是局部的，那种方式不仅导致内存使用率不高，而且也无法消除用户因为Session回收而出现随机掉线的风险。

如果读者非常希望借助memcached提高Session读取速度，那么可以借鉴Norkūnas提出的memcached+RDBMS（在有些情况下，NoSQL也可以）的模式：

当用户登录时，将Session “set”到memcached，并写入数据库；
在Session中增加一个字段，标识Session最后写入数据库的时间；
每个页面加载的时候，优先从memcached读取Session，其次从数据库读取；
每加载N页或者Y分钟后，再次将Session写入数据库；
从数据库中获取过期Session，优先从memcached中获取最新数据。


  [1]: https://imgs.gnux.cn/usr/uploads/2015/03/3136632469.jpg