---
title: "[翻译]Play框架1.2.7版本教程"
categories: [ "Java" ]
tags: [ "play1.x","play教程" ]
draft: false
slug: "java-play127-tutor-list"
date: "2014-11-06 18:42:00"
---

   Play 框架是一个完整的 Web 应用开发框架，覆盖了 Web 应用开发的各个方面。它借鉴了流行的 Ruby on Rails 和 Grails 等框架，又有自己独有的优势。具体表现在以下几个方面：其一，通过 Play 框架提供的命令行工具，可以快速创建Java Web 应用。其二，它拥有Java 代码动态编译机制，在修改代码之后，不需要重启服务器就可以直接看到修改之后的结果。其三，它还使用 JPA 规
范来完成领域对象的持久化，可以很方便的使用不同的关系数据库作为后台存储。其四，它使用 Groovy 作为视图层模板使用的表达式语言。模板之间的继承机制避免了重复的代码。总的来说，Play 框架非常适合快速创建Web 应用开发。本文将为有一定Java Web框架基础的读者，来重点介绍如何使用play框架来编制一个最简单的信息增删改查应用。


<!--more-->


感谢*[spacewander](http://segmentfault.com/u/spacewander)* 的翻译
 1. [Play框架1.2.7版本教程(1) 开始项目](http://segmentfault.com/blog/spacewander/1190000000575009)
 2. [Play框架1.2.7版本教程(2) 数据模型的首次迭代](http://segmentfault.com/blog/spacewander/1190000000578205)
 3. [Play框架1.2.7版本教程(3) 建立第一个页面](http://segmentfault.com/blog/spacewander/1190000000578466)
 4. [Play框架1.2.7版本教程(4) 浏览和提交评论](http://segmentfault.com/blog/spacewander/1190000000578466)
 5. [Play框架1.2.7版本教程(5) 设置验证码](http://segmentfault.com/blog/spacewander/1190000000584517)
 6. [Play框架1.2.7版本教程(6) 对添加标签的支持](http://segmentfault.com/blog/spacewander/1190000000584661)
 7. [Play框架1.2.7版本教程(7) 通过CRUD来实现一个基本的管理](http://segmentfault.com/blog/spacewander/1190000000585257)
 8. [Play框架1.2.7版本教程(8) 添加验证](http://segmentfault.com/blog/spacewander/1190000000588763)
 9. [Play框架1.2.7版本教程(9)自定义编辑区域](http://segmentfault.com/blog/spacewander/1190000000588769)
 10. [Play框架1.2.7版本教程(10) 完成应用测试](http://segmentfault.com/blog/spacewander/1190000000588804)
 11. [Play框架1.2.7版本教程(11) 部署应用](http://segmentfault.com/blog/spacewander/1190000000589643)
 12. [Play框架1.2.7版本教程(12) 国际化和本地化](http://segmentfault.com/blog/spacewander/1190000000589655)

**附录：**
 - [配置你喜欢的 IDE](http://segmentfault.com/blog/spacewander/1190000000575009)