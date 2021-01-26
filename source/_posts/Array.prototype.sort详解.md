---
title: "Array.prototype.sort详解"
categories: [ "JS" ]
tags: [ "javascript","array","sort" ]
draft: false
slug: "detailed-arrayprototypesort"
date: "2010-11-22 12:04:00"
---

ECMAScript中的Array类有一个排序方法，即本文主要讨论的`Array.prototype.sort`（下面简称sort），它的调用是很简单的：

    var arr = [11, 2, 1];
    alert(arr.sort());

然而，上面程序的输出结果是 "1,11,2" ，而不是我们期望的"1,2,11"，这是因为在默认情况下，**sort方法排序时会把元素临时转成字符串再比较**。要解决这个问题，就要给sort传递一个参数，这个参数是一个进行元素比较的函数，其原型如下：

function compareFn(x, y)

它接受两个参数 x 和 y 。如果 x < y ，返回负数；如果 x == y，返回0；如果 x > y ，返回正数。如果要判断数字间的大小关系，直接做减法运算即可：


<!--more-->


    // 正序
    function sortAsc(x, y) {
    　　return x - y;
    }
    // 反序
    function sortDesc(x, y) {
    　　return y - x;
    }
    var testArr = [11, 2, 1];
    alert(testArr.sort(sortAsc));
    alert(testArr.sort(sortDesc));

此外，还可以实现随机排序：

    function sortByRandom(x, y) {
    　　return Math.floor(Math.random() * 2 - 1 );
    }
    alert(testArr.sort(sortByRandom));

由此可见，sort可以满足绝大部分排序需求，基本上没必要另外写排序算法了。