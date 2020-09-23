---
title: 如何理解对象继承 [JS基础增强]
date: 2019-04-12 08:21:00
updated: 2019-09-17 13:56:34
tags: 
- gimp
- 调色板
categories: 
- default

---
学习过Java、C++等面向对象的语言的同学应该都知道，面向对象的语言最大的特征就是类以及类的`封装性`、`继承性`、`多态性`。JavaScript是一门基于对象的语言，可以说在JavaScript中一切皆对象，它是基于类的动态语言，本身不提供class的实现，虽然在ES6中引入了class，但是它仍然是基于原型的，JavaScript也可以模拟实现类似Java的继承特性。之前也回顾过JavaScript中的原型与原型链，我们知道通过原型链可以实现对象的继承。每个实例对象都有一个属性proto指向它的原型，它的原型作为对象也有一个proto原型对象，一层一层向上，直到一个对象的原型对象为null，null没有原型，并且是原型链上的最后一个环节。

主要回顾一下在JavaScript中实现继承的方式，以及不同方式的有优点和缺点。

## 基于原型链的继承
第一种要讲的继承方式视基于原型链的继承，简单地来说就是将子类的构造函数原型指向父类的实例对象，举个例子：
```javascript
function Super() {
    this.x = 1;
    this.y = 'super classs';
    this.arr = [1, 3, 5];
}

function Sub() {
    this.z = 'sub class';
}

Sub.prototype = new Super();
var sub1 = new Sub();
console.log(sub1.x); // => 1
console.log(sub1.y); // => 'super class'

var sub2 = new Sub();
sub2.arr.push(7);
console.log(sub1.arr); // => [1, 3, 5, 7]
```
通过上述例子，我们可以发现基于原型链实现继承后，子类就拥有了父类的属性，这种实现方式的优点就是简单方便，易于实现，缺点就是这样的写法，子类的所有实例对象共享一份引用类型的父类的属性，修改某个子类实例的引用类型属性会直接影响到其它子类实例，还有就是实例化子类对象的时候，无法对构造函数进行传参。为了解决这些问题，所以我们接下来看另一种继承方式。


<!--more-->


## 基于构造函数的继承
基于构造函数的继承，简单理解就是借助JavaScript中的**this来实现父类对象属性的共享**，在子类构造函数中调用父类的构造函数，同时改变this的指向，看下面的例子：

```javascript
function Super(name) {
    this.x = 1;
    this.y = 2;
    this.name = name;
    this.arr = [1, 3, 5];
    this.testfunc = function() { console.log(this.name) }
}

function Sub(name) {
    this.z = 3;
    Super.call(this, name);
}

var sub1 = new Sub('sub1');
var sub2 = new Sub('sub2');
console.log(sub1.x); // => 1
console.log(sub1.name); // => 'sub1'
sub1.x = 2;
console.log(sub2.x); // => 1
console.log(sub1.testfunc === sub2.testfunc); // => false
console.log(sub1.arr === sub2.arr); // => false
```

通过构造函数方式结合call方法实现的继承，优点是解决了子类实例共享一份引用类型的父类属性的问题，所有子类实例对象互不干扰，也解决了实例化子类对象时不能传参的问题，但缺点就是如果父类属性的方法没有被所有子类实例共用的话，也会造成内存的浪费，因为每个子类实例都有自己的一份新的方法，为解决这个问题，我们接下来看下一种实现继承的方式。

## 基于原型链与构造函数的组合继承

上面的新问题主要是父类方法没有复用的问题，每个子类都有自己的新的方法，我们同时结合原型继承与构造函数继承的优点就能解决这个问题，看下面的例子：

```javascript
function Super(name) {
    this.x = 1;
    this.y = 2;
    this.name = name;
    // this.testfunc = function() { console.log(this.name) }
}

 Super.prototype.testfunc1 = function() { console.log('testfunc1') }
Super.prototype.testfunc2 = function() { console.log('testfunc2') }

function Sub(name) {
    this.z = 3;
    Super.call(this, name);
}

Sub.prototype = new Super();

var sub1 = new Sub('sub1');
var sub2 = new Sub('sub2');
console.log(sub1.testfunc1 === sub2.testfunc1); // => true
console.log(sub1.testfunc2 === sub2.testfunc2); // => true
```

从上述例子的运行结果来看，已经解决了父类方法没有被复用的问题，但是这种写法仍旧有缺点，也就是会存在两份父类属性，有一份是多余的，一定程度上会造成内存的浪费，这两份属性分别是哪两份呢？一份是将子类的原型指向父类实例的时候调用了父类的构造函数，这时会生成一份父类属性，另一份就是在子类构造函数中通过call方法调用父类的构造函数，将父类的属性复制一份到子类构造函数，这一份属性是所有子类实例对象所共享的，所以多余的是实例化父类对象的时候生成的属性。这个问题有办法解决吗，答案是有的，就是通过寄生组合继承的方式，在看寄生组合继承之前，我们先来看一下原型式继承。

## 原型式继承
原型式继承就是让子类构造函数的prototype指向父类的一个实例，看下面的例子：

```javascript
function Super() {
    this.x = 1;
    this.y = 2;
}

Super.prototype.testfunc1 = function() { console.log('testfunc1') }
Super.prototype.testfunc2 = function() { console.log('testfunc2') }

var Sub = function(sup) {
    var F = function() {}
    F.prototype = sup;
    return new F();
}

var super_ = new Super();
var sub1 = Sub(super_);
var sub2 = Sub(super_);
sub1.x = 3;
console.log(sub2.x); // => 1
console.log(sub1.testfunc1 === sub2.testfunc1) // => true
super_.x = 5;
console.log(sub1.x); // => 3
console.log(sub2.x); // => 5
console.log(sub1);
console.log(sub2);
```

最后的执行结果如下图所示：
![js_super_prototype_01.png][1]
从运行结果我们可以发现，原型式的继承跟基于原型链的继承其实是一样的，我们也可以看到我们修改sub1.x的值值时，sub2.x的值并未发生改变（这个值等于sub2原型上的x值），也就是说给sub1.x赋值是在sub1上添加x属性，而不是修改原型上的属性x。

## 寄生式继承
寄生式继承其实就是在原型式继承对的基础上再封装一层，看下面的例子：
```javascript
function Super() {
    this.x = 1;
    this.y = 2;
}

Super.prototype.testfunc1 = function() { console.log('testfunc1') }
Super.prototype.testfunc2 = function() { console.log('testfunc2') }

var Sub = function(sup) {
    var dest = (function() {
        var F = function() {}
        F.prototype = sup;
        return new F();
    })();
    dest.hello = 'hello';
    dest.testfunc = function(){}
    return dest;
}
var super_ = new Super();
var sub1 = Sub(super_);
var sub2 = Sub(super_);
console.log(sub1.hello); // => 'hello'
console.log(sub2.hello); // => 'hello'
console.log(sub1.testfunc === sub2.testfunc); // => false
```
在原型式继承的基础上，我们再封装一层后，可以继续给子类添加子类独有的属性(有点像构造函数式继承)，从例子中的sub1.testfunc === sub2.testfunc 返回false我们也可以知道，引用类型的父类属性也是没有被复用，每个子类实例都会拥有自己的一份引用类型属性testfunc，对于引用类型的属性需要像testfunc1和testfunc2那样添加到父类的原型上。

## 寄生组合继承
这种继承方式说简单点就是避免调用两次父类构造函数，我们可以通过复用父类原型的一个副本来实现子类实例对象共用父类属性，也就是将子类的原型指向父类原型的副本，举个例子：

```javascript
function Super(name) {
    this.x = 1;
    this.y = 2;
    this.name = name;
}

Super.prototype.testfunc1 = function() { console.log('testfunc1') }
Super.prototype.testfunc2 = function() { console.log('testfunc2') }

function Sub(name) {
    this.z = 3;
    Super.call(this, name);
}

var prototype = (function(Super) { // 父类原型的副本
    var F = function(){}
    F.prototype = Super;
    return new F();
})()
// var prototype = Object.create(Super.prototype); // 也可以用这种写法
prototype.constructor = Sub; 
Sub.prototype = prototype;

var sub = new Sub('sub');
console.log(sub.x); // => 1
```

这种写法先是通过Object.create创建了一个父类原型的副本prototype，此时prototype的constructor属性是指向Super构造函数的，因为下一步我们需要把子类的原型指向这个父类原型副本，所以我们也需要把父类原型副本的constructor属性指向子类构造函数，从而实现了对父类属性的继承。这种继承方式的优点就是解决了存在两份父类属性的问题。

JavaScript继承是一个非常庞大的话题，写这篇博客也查阅了很多资料，但是肯定仍旧存在理解得不准确的地方，望大佬指正。

作者：xiaoweihuang
链接：https://www.xiaoweihuang.me/2018/11/28/about-object-inheritance/


  [1]: https://imgs.gnux.cn/usr/uploads/2019/04/2060014807.png