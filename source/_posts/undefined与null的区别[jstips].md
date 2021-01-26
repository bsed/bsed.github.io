---
title: "undefined与null的区别[jstips]"
categories: [ "JS" ]
tags: [ "javascript","jstips","undefined","nulll" ]
draft: false
slug: "the-difference-between-undefined-and-null"
date: "2016-10-01 09:45:00"
---

> 理解undefined与null的区别。

 - undefined表示一个变量没有被声明，或者被声明了但没有被赋值
 - null是一个表示“没有值”的值
 - Javascript将未赋值的变量默认值设为undefined
 - Javascript从来不会将变量设为null。它是用来让程序员表明某个用var声明的变量时没有值的。
 - undefined不是一个有效的JSON，而null是
 - undefined的类型(typeof)是undefined
 - null的类型(typeof)是object. 为什么?

## 它们都是基本类型


<!--more-->


他们都是 `false (Boolean(undefined) // false, Boolean(null) // false)`
你可以这样判断一个变量是否是 undefined
```javascript
  typeof variable === "undefined"
```
你可以这样判断一个变量是否是 null
```javascript
  variable === null
```
双等号比较时它们相等，但三等号比较时不相等
```javascript
  null == undefined // true
  null === undefined // false
```

原文: http://www.jstips.co/en/differences-between-undefined-and-null/