---
title:  Angular表达式 [Ionic Angularjs 学习笔记]
date: 2015-09-03 20:57:00
updated: 2015-09-04 20:59:47
tags: 
- css
- dropdowns
categories: 
- css

---
表达式在AngularJS应用中被广泛使用,因此深入理解AngularJS如何使用并运算表达式是非 常重要的。

前面已经见过使用表达式的示例。用{{ }}符号将一个变量绑定到$scope上的写法本质上就 是一个表达式:{{ expression }}。当用$watch进行监听时,AngularJS会对表达式或函数进行 运算。

表达式和eval(javascript)非常相似,但是由于表达式由AngularJS来处理,它们有以下显 著不同的特性:
- 所有的表达式都在其所属的作用域内部执行,并有访问本地$scope的权限;  如果表达式发生了TypeError和ReferenceError并不会抛出异常;
- 不允许使用任何流程控制功能(条件控制,例如if/eles);
- 可以接受过滤器和过滤器链。


<!--more-->

