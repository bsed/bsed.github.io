---
title: JavaScript生成GUID字符串
date: 2013-03-20 13:44:00
updated: 2015-03-02 13:42:55
tags: 
- jvm
categories: 
- java

---
*什么是GUID*全局唯一标识符（GUID，Globally Unique Identifier）也称作 UUID(Universally Unique IDentifier) 。GUID是一种由算法生成的二进制长度为128位的数字标识符。GUID主要用于在拥有多个节点、多台计算机的网络或系统中。GUID 的格式为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”，其中的 x 是 0-9 或 a-f 范围内的一个32位十六进制数。在理想情况下，任何计算机和计算机集群都不会生成两个相同的GUID。GUID 的总数达到了2^128（3.4×10^38）个，所以随机生成两个相同GUID的可能性非常小，但并不为0。GUID一词有时也专指微软对UUID标准的实现。[/newtext]


<!--more-->


在C#与SqlServer结合的分布式项目中，数据库通常使用GUID 类型的数据作为主键，这也就要求数据库或者C#程序可以主动的生成GUID ，在C#中，使用
`Guid.NewGuid()`
即可产生一个Guid 类型的对象，在SqlServer中也可以使用
`NEWID()`

函数来产生一个新的GUID ，但是无论是C#还是SqlServer 都属于服务端程序，需要消耗服务端的资源，那么Guid的产生可不可以放在客户端呢？

但是在JavaScript中并没有找到产生Guid 相关的方法，只能自己去实现。在网上百度一番，找到如下代码：

    function newGuid() {
                var guid = "";
                for (var i = 1; i <= 32; i++) {
                    var n = Math.floor(Math.random() * 16.0).toString(16);
                    guid += n;
                    if ((i == 8) || (i == 12) || (i == 16) || (i == 20))
                        guid += "-";
                }
                return guid;
            }

代码来自：http://hi.baidu.com/haofz1983/item/a9db11cb3113e2d597445249