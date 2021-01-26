---
title: "为什么要使用数据网格Data Grid"
categories: [ "Java" ]
tags: [ "高性能","Data Grid","数据网格" ]
draft: false
slug: "why-use-a-data-grid-data-grid"
date: "2015-01-04 17:29:00"
---

本文讨论Java的数据网格如何实现高性能，高可用 高伸缩和高可靠性的。

当建立一个大型Java应用时，引起性能问题大部分是延迟，延迟是指请求和响应之间的时间差，在一个分布式Java系统中引起延迟的原因有：

 1. 从磁盘上加装数据的IO延迟
 2. 跨网络加装数据的IO延迟。
 3. 在分布式锁上的资源争夺。
 4. 垃圾回收引起的暂停。

典型Ping时间是：本地机器是57µs；局域网是300 µs；从伦敦到纽约是100ms；对于1Gb网络，网络数据传输是每秒25MB – 30MB。对于10GB网络是每秒250MB – 350MB。使用SATA 3.0接口的SSD硬盘数据传输是每秒500-600MB。如果你有1G以上数据需要处理，磁盘延迟会严重影响应用性能。


<!--more-->


硬件上最低延迟是内存，典型的内存缓存是每秒3-5 GB，能够随着CPU扩展。如果你有两个[处理器](http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&u=http%3A%2F%2Fwww%2Ejdon%2Ecom%2Fartichect%2Fdata%2Dgrid%2Ehtml&p=baidu&c=news&n=10&t=tpclicked3_hc&q=banq_cpr&k=%B4%A6%C0%ED%C6%F7&k0=%C0%AC%BB%F8%BB%D8%CA%D5&kdi0=1&k1=java&kdi1=8&k2=%B4%E6%B4%A2&kdi2=8&k3=hotel&kdi3=8&k4=%B4%A6%C0%ED%C6%F7&kdi4=8&sid=370e6a5f680ae4b4&ch=0&tu=u1683405&jk=1a41b083fb761cab&cf=29&fv=16&stid=9&urlid=0&luki=5&seller_id=1&di=128)，你就能每秒10GB，如果有4CPU就能获得 20GB. 有一个内存基准测试称为STREAM (http://www.cs.virginia.edu/stream/) 是测试许多计算机的内存吞吐量，一些在大量CPU帮助下能够实现每秒TB级别的吞吐量。

因此可以总结如下：

 - 内存是快的: 为了高性能，你需要在内存中处理数据。
 - 网络是慢的: 通过网络传输数据会严重影响性能，包括数据库连接池。

现在的问题是在内存中处理几个G的数据是否可行？因为内存价格下降，我们可以购买单个服务器有1TB大小的内存。另外一个提升是CPU核数提升，

### 并行是未来 ###
![data-grid.png][1]

数据网格中多分区之间的复制集群相当于你能存储更多数据到内存中，访问时间是和数据网格中的JVM数量无关的。

举例，如果我们有20个JVM节点，每个有4GB的空heap可用于[存储](http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&u=http%3A%2F%2Fwww%2Ejdon%2Ecom%2Fartichect%2Fdata%2Dgrid%2Ehtml&p=baidu&c=news&n=10&t=tpclicked3_hc&q=banq_cpr&k=%B4%E6%B4%A2&k0=%C0%AC%BB%F8%BB%D8%CA%D5&kdi0=1&k1=java&kdi1=8&k2=%B4%E6%B4%A2&kdi2=8&k3=hotel&kdi3=8&k4=%B4%A6%C0%ED%C6%F7&kdi4=8&sid=370e6a5f680ae4b4&ch=0&tu=u1683405&jk=1a41b083fb761cab&cf=29&fv=16&stid=9&urlid=0&luki=3&seller_id=1&di=128)对象，考虑重复后，将能存储40GB个Java对象，如果我们再增加20 JVM节点，我们就能存储80GB. 而访问时间是一直不变的。读写网格中的对象将是直接访问与对象主键key相关的那个对象。

JSR 107 定义一个数据网格的标准API，非常类似[java](http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&u=http%3A%2F%2Fwww%2Ejdon%2Ecom%2Fartichect%2Fdata%2Dgrid%2Ehtml&p=baidu&c=news&n=10&t=tpclicked3_hc&q=banq_cpr&k=java&k0=%C0%AC%BB%F8%BB%D8%CA%D5&kdi0=1&k1=java&kdi1=8&k2=%B4%E6%B4%A2&kdi2=8&k3=hotel&kdi3=8&k4=%B4%A6%C0%ED%C6%F7&kdi4=8&sid=370e6a5f680ae4b4&ch=0&tu=u1683405&jk=1a41b083fb761cab&cf=29&fv=16&stid=9&urlid=0&luki=2&seller_id=1&di=128).util.Map API ：

    public static void main( String[] args )
        {
            CacheManager CacheManager = Caching.getCachingProvider().getCacheManager();
            MutableConfiguration<String, String> config = new MutableConfiguration<String, String>();
            CacheManager.configureCache("C2B2",config);
            Cache Cache = CacheManager.getCache("C2B2");
            Cache.put("Key", "Value");
            System.out.println(Cache.get("Key"));
        }

许多数据网格也利用Java NIO来存储Java对象在Heap之外的Java NIO Buffer中，这样做的优点是在不增加垃圾回收导致暂停的情况下增加可用内存。

### 网格中并行处理 ###

许多数据网格也利用Java NIO来存储Java对象在Heap之外的Java NIO Buffer中，这样做的优点是在不增加垃圾回收导致暂停的情况下增加可用内存。

当我们跨网格在许多JVM中存储许多10GB的对象时，需要跨这些数据运行一些处理时会有问题，比如我们将存储的对象比喻成旅馆，它们是在一定日期中才可用，比如如果我们希望查询2015情人节在巴黎那天所有可预订的旅馆，代码如下

    public static void main( String[] args )
        {
            CacheManager CacheManager = Caching.getCachingProvider().getCacheManager();
            MutableConfiguration<String, String> config = new MutableConfiguration<String, String>();
            CacheManager.configureCache("ParisHotels",config);
            Cache hotelCache = CacheManager.getCache("ParisHotels");
            Date valentinesDay = new Date(2015,2,14); // I know it is deprecated
            for (String hotelName : hotelNames ) {
                    Hotel hotel = (Hotel)hotelCache.get(hotelName);
                    if (hotel.isAvailable(valentinesDay)){
                            System.out.println("Hotel is available" + hotel);
                    }
            }
    }

在一个数据网格中，对象是分布存储在不同大量的JVM中，每次get调用需要通过网络序列化传输过来对象，使用上面这张拉10GB级别量的数据是非常慢的。

大多数Java数据网格产品允许实现预处理。取代上面通过拉数据的方式，它们会发送这段处理代码到各个JVM中，然后在JVM内部并行执行这段代码，当然这对代码大小有所限制，最好不超过几KB。

跨JVM并行处理可以充分利用所有的CPU核数，比如上面查询旅馆的案例，使用Oracle Coherence这样一个流行的Data Grid 产品，只要继承EntryProcessor的代码将被序列化到数据网格的所有节点上。JSR107中也有EntryProcessor概念，将是所有Data Grid产品的标准了。


<!--more-->


 

    Public class HotelSearch implements EntryProcessor {
           HotelSearch(Date availability) {
                  this.availability = availability;
           }
           Map processAll(Set hotels) {
                  Map mapResults = new ListMap();
                  for (Entry entry : hotels) {
                         Hotel hotel = (Hotel)entry.getValue();
                         if (hotel.isAvailable(this.availability)) {
                         }       
                  }
           }
    }
    public static void main( String[] args )    {
            NamedCache hotelCache = CacheFactory.getCache("ParisHotels");
           Date valentinesDay = new Date(2015,2,14); // I know it is deprecated
           Map results = hotelCache.processAll((Filter)null, new HotelSearch(valentinesDay);
    }

 
  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/1320340130.png