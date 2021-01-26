---
title: "排列含音节字母的字符串[jstips]"
categories: [ "JS" ]
tags: [ "javascript","sort","jstips" ]
draft: false
slug: "string-jstips-with-the-syllable-letter"
date: "2016-09-30 09:38:00"
---

> javascript有一个原生方法
> [sort](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)
> 可以排列数组。一次简单的 `array.sort()`
> 将每一个数组元素视为字符串并按照字母表排列。你也可以提供[自定义排列](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#Parameters)方法。

```javascript
['Shanghai', 'New York', 'Mumbai', 'Buenos Aires'].sort();
// ["Buenos Aires", "Mumbai", "New York", "Shanghai"]
```
但是当你试图整理一个如['é', 'a', 'ú', 'c']这样的非ASCII元素的数组时，你可能会得到一个奇怪的结果['c', 'e', 'á', 'ú']。这是因为排序方法只在英文下有用。


<!--more-->


**看一下下一个例子:**
```javascript
// 西班牙语
['único','árbol', 'cosas', 'fútbol'].sort();
// ["cosas", "fútbol", "árbol", "único"] // bad order

// 德语
['Woche', 'wöchentlich', 'wäre', 'Wann'].sort();
// ["Wann", "Woche", "wäre", "wöchentlich"] // bad order
```
幸运的是，有两种方法可以解决这个问题，由ECMAScript国际化API提供的 [localeCompare](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare)和[Intl.Collator](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Collator)。
两个方法都有自定义配置参数可以使其更好用。

## 使用localeCompare()
```javascript
['único','árbol', 'cosas', 'fútbol'].sort(function (a, b) {
  return a.localeCompare(b);
});
// ["árbol", "cosas", "fútbol", "único"]

['Woche', 'wöchentlich', 'wäre', 'Wann'].sort(function (a, b) {
  return a.localeCompare(b);
});
// ["Wann", "wäre", "Woche", "wöchentlich"]
```
## 使用Intl.Collator() 

```javascript
['único','árbol', 'cosas', 'fútbol'].sort(Intl.Collator().compare);
// ["árbol", "cosas", "fútbol", "único"]

['Woche', 'wöchentlich', 'wäre', 'Wann'].sort(Intl.Collator().compare);
// ["Wann", "wäre", "Woche", "wöchentlich"]
```
两个方法都可以自定义区域位置。

 - 根据[Firefox](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare#Performance)，当比较大数量的字符串时，使用Intl.Collator更快。
 - 所以当你处理一个由非英语组成的字符串数组时，记得使用此方法来避免排序出现意外。

原文:[http://www.jstips.co/en/sorting-strings-with-accented-characters/](http://www.jstips.co/en/sorting-strings-with-accented-characters/)