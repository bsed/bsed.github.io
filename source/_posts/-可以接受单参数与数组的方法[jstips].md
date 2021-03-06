---
title: " 可以接受单参数与数组的方法[jstips]"
categories: [ "JS" ]
tags: [ "javascript","jstips" ]
draft: false
slug: "method-of-single-parameter-and-array-can-be-accepted-jstips"
date: "2016-10-02 09:55:00"
---

写一个方法可以接受单个参数也可以接受一个数组，而不是分开写两个方法。这和jQuery的一些方法的工作原理很像(css 可以修改任何匹配到的选择器).
你只要把任何东西连接到一个数组. `Array.concat` 可以接受一个数组也可以接受单个参数。

```javascript
function printUpperCase(words) {
  var elements = [].concat(words || []);
  for (var i = 0; i < elements.length; i++) {
    console.log(elements[i].toUpperCase());
  }
}
```


<!--more-->


**printUpperCase** 现在可以接受单个单词或多个单词的数组作为它的参数。同时也可以避免在不传递参数时抛出的TypeError错误的隐患。

```javascript
printUpperCase("cactus");
// => CACTUS
printUpperCase(["cactus", "bear", "potato"]);
// => CACTUS
//  BEAR
//  POTATO
```

原文:http://www.jstips.co/en/writing-a-single-method-for-arrays-and-a-single-element/