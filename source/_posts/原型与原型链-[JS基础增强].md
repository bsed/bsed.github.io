---
title: "原型与原型链 [JS基础增强]"
categories: [ "JS" ]
tags: [ "prototype","原型链","JS基础增强" ]
draft: false
slug: "prototype-and-prototype-chain-js-foundation-enhancement"
date: "2019-03-27 14:00:00"
---

说到基础知识，其实很重要，在面试的过程中也基本都会被问到。俗话说，万丈高楼平地起。没有足够扎实的基础，是不可能筑起高楼大厦的。所以为了巩固自己的基础知识，我决定对JavaScript的各个知识点进行深入理解和总结，今日要回顾和探索的是`原型`与`原型链`。

## 构造函数创建对象
我们先使用构造函数创建一个对象：
```javascript
function Dog(){}
var dog = new Dog();
dog.name = 'MBIOQ';
console.log(dog.name); // MBIOQ
```
在这个例子中，Dog就是一个构造函数，我们通过new创建了一个实例对象dog。


<!--more-->


##原型prototype
每个函数都有一个`prototype`属性，看下面的一个例子：
```javascript
function Dog(){}
Dog.prototype.name = 'MBIOQ';
var dog1 = new Dog();
var dog2 = new Dog();
console.log(dog1.name); // MBIOQ
console.log(dog2.name); // MBIOQ
```
我们通过构造函数Dog创建了两个实例dog1和dog2，然后分别打印这两个实例的name属性，发现打印结果是相同的，都是从Dog的prototype属性指向的对象继承而来，那么函数的prototype属性是指向谁呢？其实，这个对象就是我们在调用的构造函数的原型对象，也就是上面例子中Dog的原型。

那么什么是原型呢？可以简单理解成：每一个对象（null除外）在创建的时候就会与之关联的另一个对象，这个对象就是原型，每一个对象都会从原型里面继承属性。

所以构造函数与原型对象的关系可以用如下图所示：
![js_prototype_01.png][1]

前面也提到，构造函数的prototype属性指向了它的原型，那么通过构造函数创建的实例对象是通过什么属性指向原型的呢？那就是我们接下来要说的`__proto__`。
```javascript
__proto__
```
在JavaScript中，每个对象被创建时，不管是`普通对象`还是`函数对象`，都拥有一个__proto__属性，这个属性也指向构造函数的原型对象。举个栗子：
```javascript
function Dog() {}
var dog = new Dog();
dog.__proto__ === Dog.prototype; // true
```
所以构造函数、实例对象与原型对象的关系可以用如下图表示：
![js_prototype_02.png][2]

既然**构造函数**和**实例对象**都有指向了**原型对象的属性**，那么原型对象有没有相关的属性能指向构造函数或者实例对象呢？

## constructor
由于一个构造函数可以创建多个实例，所以原型对象是没有指向实例对象的属性的，但是有一个属性指向了构造函数。这个属性就是constructor，为了验证这一个结论，我们可以通过以下例子进行验证：
```javascript
function Dog() {}
console.log(Dog === Dog.prototype.constructor) // true
```
到了这里，**构造函数**、**实例对象**与**原型对象**的关系可以更新为如下图所示：
![js_prototype_03.png][3]

## 原型链

由于原型对象也是一个对象，我们可以试着探索，这个对象又是怎么创建而来的呢？

我们在浏览器控制台执行如下代码：
```javascript
function Dog() {}
console.log(Dog.prototype.__proto__)
```
运行结果如下：

![js_prototype_04.png][4]

在前面我们已经得出了结论，实例对象的`__proto__`属性指向了**构造函数的原型对象**，也就是说，刚刚在浏览器控制台打印出来的对象就是`Dog.prototype`的构造函数的原型对象，由于构造函数的原型对象的constructor属性又指向了与之关联的构造函数，所以我们从控制台打印出来的结果可以看出，`Dog.prototype.__proto__`的constructor属性是**Object对象**，所以我们可以把构造函数、实例对象与原型对象跟Object的关系用下图表示：
![js_prototype_05.png][5]

同样地，我们在浏览器控制台打印一下`Object.prototype.__proto__`,输出的结果是`null`,由于`__proto__`是任何对象都有的属性，在JavaScript中一切都是对象，所以会有一条通过`__proto__`连起来的链条，递归访问`__proto__`最终会到尽头，尽头的值就是null,当JavaScript引擎查找对象的属性时，先在对象自身查找是否存在该属性，如果不存在，就会沿着__proto__原型链查找，但不会查找自身的prototype，直到找到顶层为止。

![js_prototype_06.png][6]


> 本文作者：xiaoweihuang 本文链接：
> [https://www.xiaoweihuang.me/2018/11/17/about-javascript-prototype/](https://www.xiaoweihuang.me/2018/11/17/about-javascript-prototype/)


  [1]: https://imgs.gnux.cn/usr/uploads/2019/03/1196975190.png
  [2]: https://imgs.gnux.cn/usr/uploads/2019/03/2146253413.png
  [3]: https://imgs.gnux.cn/usr/uploads/2019/03/2090517483.png
  [4]: https://imgs.gnux.cn/usr/uploads/2019/03/1993846469.png
  [5]: https://imgs.gnux.cn/usr/uploads/2019/03/1253257523.png
  [6]: https://imgs.gnux.cn/usr/uploads/2019/03/3500520131.png