---
title: "Swift - 类和结构体的区别"
categories: [ "IOS" ]
tags: [ "swift" ]
draft: false
slug: "swift-classes-and-structures"
date: "2015-10-30 09:43:00"
---

类和结构体有许多相同之处，也有许多不同之处。

二者区别如下：

 1. 类可以继承，结构体不可以
 2. 可以让一个类的实例来反初始化，释放存储空间，结构体做不到
 3. 类的对象是引用类型，而结构体是值类型。所以类的赋值是传递引用，结构体则是传值。

相同点：


<!--more-->


 1. 类和结构体都可以扩展
 2. 定义属性用于储存值
 3. 定义方法用于提供功能
 4. 定义下标用于通过下标语法访问值
 5. 定义初始化器用于生成初始化值