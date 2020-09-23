---
title: EcmaScript6教程: Array
date: 2015-10-31 22:31:00
updated: 2015-10-31 22:31:48
tags: 
- Sublime-Text3
- LiveReload
categories: 
- js

---
数组是我们在项目中用的很频繁的类型，很多人也尝试写了一些处理数组的框架。ES5新加了很多Array方法，比如说 `isArray` 、`every` 、`forEach` 和 `indexOf` 等等，这些方法帮助我们提高了编码效率，但如果你需要兼容低版本浏览器，那只能使用underscore或者lodash了。

## 目录

    Array.from
    Array.of
    Array.prototype.fill
    Array.prototype.find
    Array.prototype.findIndex
    Array.prototype.entries
    Array.prototype.keys
    Array.prototype.values

## Array.from


<!--more-->


`Array.from` 是一个数组静态方法，它可以把一个类数组或者可迭代对象转成一个新的数组实例，类数组大家都知道，其实他不算是一个数组，他只是一个带有length属性的对象，而通常我们都会将它转成一个真正的数组，在很多框架中都是这样做的

    function toArray(arrayLike) {
      return [].splice.call(arrayLike, 0);
    }

现在我们不用特意写一个方法去转化了。比如说我们在函数内部使用arguments，

    function a() {
      return Array.from(arguments);
    }
    
    a(2, 3, 4);
    // [2, 3, 4]

不仅仅是类数组，它还可以转化Set和Map类型。

Array.from有一个可选参数mapFn，它允许你遍历新生成的数组并执行一些表达式:

    var s = new Set(['a', 'b', 'c']);
    Array.from(s, function(x) {
      return x + 'e';
    });
    // ['ae', 'be', 'ce']

## Array.of

Array.of也是静态方法，它可以把多个变量合并成一个数组。

    Array.of(1, 2, 3); // [1, 2, 3]

我们也可以实现Array.of方法:

    if(!Array.of) {
      Array.of = function() {
        return Array.prototype.slice.call(arguments);
      }
    }

## Array.prototype.fill

fill()方法可以填充数组里的元素，这个方法在如今最新chrome浏览器中还未支持

    [1, 2, 3].fill(8);  // [8, 8, 8]
    [1, 2, 3].fill(8, 1);  // [1, 8, 8]

## Array.prototype.find

`find()` 方法返回一个数组被查找规则匹配的第一个元素，如果没找到，返回undefined。

    console.log([false, true].find(true)); // true
    
    console.log([1, 2, 3].find(function(item) {
      return item > 1;
    })) // 2

## Array.prototype.findIndex

`findIndex()` 返回数组被查找规则匹配元素的索引，如果没有匹配，返回-1，有点类似indexOf，但比indexOf更高级。

    console.log([false, true].findIndex(true)); // 1
    
    console.log([1, 2, 3].find(function(item) {
      return item > 1;
    })) // 1

## Array.prototype.entries

`entries()`将数组转成键值对组，这个方法返回的是generator，对于generator，下次我会做详细介绍。

    var arrGenerator = [1, 2, 3].entries();
    
    console.log(arrGenerator.next().value) // [0, 1]
    console.log(arrGenerator.next().value) // [1, 2]
    console.log(arrGenerator.next().value) // [2, 3]

## Array.prototype.keys

`keys()` 返回数组每个元素的键，它也是一个generator。

    var arr = [1, 2, 3];
    var iterator = arr.keys();
    
    console.log(iterator.next()); // {value: 0, done: false}
    console.log(iterator.next()); // {value: 1, done: false}
    console.log(iterator.next()); // {value: 2, done: false}
    console.log(iterator.next()); // {value: undefined, done: true}

## Array.prototype.values

和keys()类似，不过values()返回的是值

    var arr = [1, 2, 3];
    var iterator = arr.values();
    
    console.log(iterator.next()); // {value: 1, done: false}
    console.log(iterator.next()); // {value: 2, done: false}
    console.log(iterator.next()); // {value: 3, done: false}
    console.log(iterator.next()); // {value: undefined, done: true}
