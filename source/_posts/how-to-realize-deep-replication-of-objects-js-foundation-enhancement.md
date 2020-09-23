---
title: 如何实现对象的深度复制 [JS基础增强]
date: 2019-04-10 08:43:00
updated: 2019-09-17 13:56:45
tags: 
- 继承
- JS基础增强
categories: 
- js

---
我们在面试或者笔试的过程中，对象的深复制问题经常会被问到，说难不难，说简单也不简单，要弄清楚这个问题，我们首先需要明确的是在JavaScript中的数据类型有哪些，通常分为两大类，一类是基本数据类型，包`Boolean`、`Null`、`Number`、`undefined`、`String`、`Symbol`，还有一类是复杂数据类型，即`Object类型`。在这篇博客中，我们主要来回顾一下对象的深度复制，也就是如何实现对复杂数据类型的深度复制。在回顾之前，我们先来看看基本数据类型的复制。

## 基本数据类型的复制
基本数据类型的复制其实就是我们通常所说的浅复制，变量复制以后，修改旧的或者复制出来的变量，另一个变量保持不变，因为基本数据类型我们是按值来访问的，操作是它们实际保存的值。举个简单的例子：
```javascript
var a = 123;
var b = a;
a = 'abc';
console.log(b); // => 123
```
为什么会这样呢？我们从内存的角度来分析这个现象，当变量被复制以后，计算机会在栈内存中开辟一块新的内存来保存复制后的值，所以复制前后的两个变量互不干涉，它们都是栈内存的一块空间，在执行上下文栈执行完这些代码后，它们就会从内存中被释放。

从上面的内存分配图简单理解一下基本数据类型的复制，当我们将a的值赋给b的时候，在栈内存中就会开辟一块新的空间用来保存b，同时b的值为123，当对a进行重新赋值为’abc’后，实际只改变了a的值，对b没有影响。同样的，如果我们对b进行重新赋值，实际也只会改变b的值，对a不会产生影响。接下来我们就探索一下，如何实现复杂数据类型的深度复制，这个深度复制也是基于基本数据类型的浅复制而言的。


<!--more-->


## 对象的复制
先来看一个简单的例子，我们按照前面复制基本数据类型的做法对对象进行浅复制，看看结果怎么样：
```javascript
var a = { x: 1 }
var b = a;
b.x = 2;
console.log(a.x) // => 2
```
从上面例子的运行结果来看，我们使用复制基本数据类型的做法来对对象进行复制是不行的，我们修改复制后对象的某个属性，直接影响到了原来的对象。原因就是我们在代码中操作对象的时候，实际上操作的是对象的引用。从内存的角度来分析就是，a变量是在栈内存中的某块空间保存，它指向堆内存的另一块空间，这块空间的值就是对象实际的值，对a对象进行复制后，会在栈内存中开辟一块新的空间保存复制后的变量b，同时b跟a指向了堆内存中的同一块空间，所以修改其中一个对象都会影响到另一个对象的值。

从内存图可以直观看出，复制后的变量b是栈内存中的另一块空间，但是它跟a指向的是堆内存中的同一块地址，当我们修改b.x的值时，a.x也会被修改。要想复制后的变量跟原来的对象互不影响，我们就需要对对象进行深度复制。如何实现深度复制呢？无非就是要让复制后的对象与原对象指向不同的堆内存空间。

实现一个能复制基本数据类型，也能深度复制对象的通用方法
首先封装一个能实现复制基本数据类型的方法：
```javascript
function isObject(obj) {
    return typeof obj === 'object' && !!obj;
}

function deepClone(obj) {
    if (!isObject(obj)) return obj;
}
```
上面的`deepClone`方法，当我们判断到数据是非对象类型或者null时，我们返回传入的变量即可以实现基本数据类型的复制，包括null的复制，但是上面判断传入的值是否是对象的方法有问题吗？还是有问题的，*假如我们传入的是一个function时，isObject执行后的返回值将是false，也就是我们执行deepClone后，返回的是传入的function*，举个简单的例子：

```javascript
function isObject(obj) {
    return typeof obj === 'object' && !!obj;
}

function deepClone(obj) {
    if (!isObject(obj)) return obj;
}

var a = function() { console.log('test') }
var b = deepClone(a);
b(); // => test
```
从输出的结果来看，好像是deepClone是实现了对象的复制，但是我们给a添加一些属性，然后使用deepClone复制出对象b，同时修改b对象的属性，看看是否会对a对象产生影响：
```javascript
function isObject(obj) {
    return typeof obj === 'object' && !!obj;
}

function deepClone(obj) {
    if (!isObject(obj)) return obj;
}

var a = function() { console.log('test') }
a.x = 1;
var b = deepClone(a);
b.x = 2;
console.log(a.x) // => 2
```
很显然，我们不能使用浅复制的方式对function进行复制，而是以对象的形式对它进行复制，所以isObject修改一下:
```javascript
function isObject(obj) {
    var type = typeof obj;
    return type === 'object' && !!obj || type === 'function';
}

function deepClone(obj) {
    if (!isObject(obj)) return obj;
}
```
通过上述代码的封装，我们就可以实现基本数据类型的复制了，接下来我们看看如何深度复制对象类型的数据。

我们知道typeof的返回值是”object”的是数据类型括数组类型、对象类型以及null，但null我们已经把它当做特例处理了，即传入null的时候，返回null即可实现复制，所以接下来思考一下如何深度复制数组、对象类型的数据。

关于数组的深度复制，我们不难想到数组的slice方法，它是返回一个新的数组，所以用这个方法即可以实现数组的深度复制：
```javascript
function isObject(obj) {
    var type = typeof obj;
    return type === 'object' && !!obj || type === 'function';
}

function deepClone(obj) {
    if (!isObject(obj)) return obj;
    if (Object.prototype.toString.call(obj) === '[object Array]') {
        return obj.slice();
    }
}
```
简单测试一下：
```javascript
function isObject(obj) {
    var type = typeof obj;
    return type === 'object' && !!obj || type === 'function';
}

function deepClone(obj) {
    if (!isObject(obj)) return obj;
    if (Object.prototype.toString.call(obj) === '[object Array]') {
        return obj.slice();
    }
}

var a = [1, 2, 3];
var b = deepClone(a);
b[0] = 0;
console.log(b) // => [0, 2, 3]
console.log(a) // => [1, 2, 3]
```
从运行结果来看，我们已经实现了数组的深度复制，接下来我们继续探讨对象的深度复制，假如我们需要深度复制下面这样一个对象，该如何做：
```javascript
{
    x: 1,
    y: 'a',
    z: {
        i: 2,
        j: {
            k: 3
        }
    }
}
```
简单思考一下，可以按如下实现对对象的深度复制：
```javascript
function isObject(obj) {
    var type = typeof obj;
    return type === 'object' && !!obj || type === 'function';
}

function deepClone(obj) {
    if (!isObject(obj)) return obj;
    if (Object.prototype.toString.call(obj) === '[object Array]') {
        return obj.slice();
    } else {
        var result = {};
        for(var key in obj) {
            result[key] = typeof obj[key] === 'object' ? deepClone(obj[key]) : obj[key];
        }
        return result;
    }
}
简单测试：

var a = {
    x: 1,
    y: 'a',
    z: {
        i: 2,
        j: {
            k: 3
        }
    }
}
var b = deepClone(a);
b.z = false;
console.log(a); // => { x: 1, y: "a", z: { i: 2, j: { k: 3 } } }
console.log(b); // => { x: 1, y: "a", z: false }
```
使用deepClone深度复制对象后，修改复制后的对象不会影响原来的对象。到了这里，我们可以实现基本数据类型、null以及对象的深度复制了。
