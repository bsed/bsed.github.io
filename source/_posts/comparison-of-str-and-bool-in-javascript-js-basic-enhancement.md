---
title: JavaScript 中 str 与bool 比较 [JS基础增强]
date: 2019-12-25 16:05:00
updated: 2019-12-22 16:06:08
tags: 
- php
- allow_url_fopen
categories: 
- php

---
## JavaScript 中 str 与bool 比较

先看下放例子：

```javascript
let str = '100';
console.log(str == true);  // false
console.log(str == false); // false
```

规范中提到布尔值比较：

> 如果 Type(x)是布尔值，则返回ToNumer(x) == y的结果
> 如果 Type(y)是布尔值，则返回 x == ToNumber(y) 的结果。

简单的讲，如果存在布尔值的情况下，会首先将布尔值转为对应的**number类型**，然后进行比较。如果比较的类型时number类型，会直接进行比较，如果是其他类型，则会发生强制类型转换。

<!--more-->

我们把上面的例子做一下简单转换：

```javascript
let str = '100';
console.log(str == Number(true));
console.log(str == Number(false));
```

首先会将`true`或者`false`转为`number类型`，值为`1`或者`0`。比较 `100 == 1` 或者 `100 == 0`。这两种情况都是返回**false**。


```javascript
//false
let str1 = '100';
if(str1 == true){
  console.log('Will not enter the judgment condition');
}
if(xx == false){
  console.log('Will not enter the judgment condition');
}

//ok
if(!!str1){
  console.log('Will enter the judgment condition');
}
if(Boolean(str1)){
  console.log('Will enter the judgment condition');
```

接下来看如下例子:

```javascript
console.log("0" == false); // true
console.log(0 == false); // true
console.log("" == false); // true
console.log([] == false); // true
console.log({} == false); // false
```

主要看一下**对象**和**数组**的情况下的结果

js的基本类型有**包装对象**，这点都比较清楚，和包装对象相对应的是拆包。

```javascript
[] == false;
```
上面代码的操作过程，其实是先让[]执行valueOf以及toString()方法，在和false进行比较。

```javascript
console.log([].valueOf(),[].toString()); // [] ""
// 相当于是 "" == false 的比较 
```
```javascript
console.log({}.valueOf(), {}.toString()); // {} "[object Object]"
// 相当于是 '[object Objecet]' == false 的比较，返回false
```

