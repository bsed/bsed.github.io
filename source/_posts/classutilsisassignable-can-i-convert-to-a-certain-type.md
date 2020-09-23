---
title: ClassUtils.isAssignable：是否可以转成某个类型
date: 2020-07-26 12:02:12
updated: 2020-07-26 12:02:12
tags: 
- spring
- security
- EnableGlobalMethodSecurity
categories: 
- java

---
**问题**：某个方法 返回值是类型Object，但是实际上返回值 可能是List<Map> 或者是 Map（也就是返回一条还是多条Map），需要判断返回值是不是List，否则不能直接遍历；

## 解决办法：


<!--more-->


工具类：ClassUtils
方法：isAssignable
参数1：Class<?> lhsType
参数2：Class<?> rhsType
**简单理解：**
参数1为 目标类型；
参数2为 不确定类型；

根据返回值 true/false来判断 rhsType 是不是 lhsType
补充：Assignable，可分配的，可转让的；
