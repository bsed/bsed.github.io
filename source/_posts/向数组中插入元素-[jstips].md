---
title: "向数组中插入元素 [jstips]"
categories: [ "JS" ]
tags: [ "javascript","jstips" ]
draft: false
slug: "insert-an-element-into-an-array"
date: "2016-09-28 22:04:00"
---

> 向一个数组中插入元素是平时很常见的一件事情。你可以使用push在数组尾部插入元素,可以用unshift在数组头部插入元素,也可以用splice在数组中间插入元素。
> 但是这些已知的方法，并不意味着没有更加高效的方法。让我们接着往下看……

![javascript_array.jpg][1]

## 向数组结尾添加元素

向数组结尾添加元素用push()很简单，但下面有一个更高效的方法

```javascript
var arr = [1,2,3,4,5];
var arr2 = [];

arr.push(6);
arr[arr.length] = 6;
arr2 = arr.concat([6]);
```


<!--more-->


两种方法都是修改原始数组。不信？看看[jsperf](http://jsperf.com/push-item-inside-an-array)

## 手机上的效率

### Android (v4.2.2)

 - arr.push(6); and arr[arr.length] = 6; 性能相同 // 3 319 694 ops/sec
 - arr2 = arr.concat([6]); 比其他两个方法慢50.61%

### Chrome Mobile (v33.0.0)

 - arr[arr.length] = 6; // 6 125 975 ops/sec
 - arr.push(6); 慢66.74%
 - arr2 = arr.concat([6]); 慢87.63%

### Safari Mobile (v9)

 - arr[arr.length] = 6; // 7 452 898 ops/sec
 - arr.push(6); 慢40.19%
 - arr2 = arr.concat([6]); 慢49.78%

**最快的为**

 1. arr[arr.length] = 6; // 平均 5 632 856 ops/sec
 2. arr.push(6); // 慢35.64%
 3. arr2 = arr.concat([6]); // 慢62.67%

## 桌面上的效率

### Chrome (v48.0.2564)

 - arr[arr.length] = 6; // 21 602 722 ops/sec
 - arr.push(6); 慢61.94%
 - arr2 = arr.concat([6]); 慢87.45%

### Firefox (v44)

 - arr.push(6); // 56 032 805 ops/sec
 - arr[arr.length] = 6; 慢0.52%
 - arr2 = arr.concat([6]); 慢87.36%

### IE (v11)

 - arr[arr.length] = 6; // 67 197 046 ops/sec
 - arr.push(6); 慢39.61%
 - arr2 = arr.concat([6]); 慢93.41%

### Opera (v35.0.2066.68)

 - arr[arr.length] = 6; // 30 775 071 ops/sec
 - arr.push(6); 慢71.60%
 - arr2 = arr.concat([6]); 慢83.70%

### Safari (v9.0.3)

 - arr.push(6); // 42 670 978 ops/sec
 - arr[arr.length] = 6; 慢0.80%
 - arr2 = arr.concat([6]); 慢76.07%

**最快的为**

 - arr[arr.length] = 6; // 平均42 345 449 ops/sec
 - arr.push(6); // 慢34.66%
 - arr2 = arr.concat([6]); // 慢85.79%

## 向数组的头部添加元素

现在我们试着向数组的头部添加元素：

```javascript
var arr = [1,2,3,4,5];

arr.unshift(0);

[0].concat(arr);
```
这里有一些小区别，unshift操作的是原始数组，concat返回一个新数组，参考[jsperf](http://jsperf.com/unshift-item-inside-an-array)

## 手机上的效率 :

### Android (v4.2.2)

 - [0].concat(arr); // 1 808 717 ops/sec
 - arr.unshift(0); 慢97.85%

### Chrome Mobile (v33.0.0)

 - [0].concat(arr); // 1 269 498 ops/sec
 - arr.unshift(0); 慢99.86%

### Safari Mobile (v9)

 - arr.unshift(0); // 3 250 184 ops/sec
 - [0].concat(arr); 慢33.67%

**最快的为**

 1. [0].concat(arr); // 平均4 972 622 ops/sec
 2. arr.unshift(0); // 慢64.70%

## 桌面上的效率
### Chrome (v48.0.2564)

 - [0].concat(arr); // 2 656 685 ops/sec
 - arr.unshift(0); 慢96.77%

### Firefox (v44)

 - [0].concat(arr); // 8 039 759 ops/sec
 - arr.unshift(0); 慢99.72%

## IE (v11)

 - [0].concat(arr); // 3 604 226 ops/sec
 - arr.unshift(0); 慢98.31%

## Opera (v35.0.2066.68)

 - [0].concat(arr); // 4 102 128 ops/sec
 - arr.unshift(0); 慢97.44%

## Safari (v9.0.3)

 - arr.unshift(0); // 12 356 477 ops/sec
 - [0].concat(arr); 慢15.17%

**最快的为**

 1. [0].concat(arr); // 平均6 032 573 ops/sec
 2. arr.unshift(0); // 慢78.65%

## 向数组中间添加元素
使用splice可以简单的向数组中间添加元素，这也是最高效的方法。
```javascript        
var items = ['one', 'two', 'three', 'four'];
items.splice(items.length / 2, 0, 'hello');
```
我在许多浏览器和系统中进行了测试，结果都是相似的。希望这条小知识可以帮到你，也欢迎大家自行测试。


  [1]: https://imgs.gnux.cn/usr/uploads/2016/09/4154770332.jpg