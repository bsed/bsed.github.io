---
title: 理解JavaScript 中的 call和apply
date: 2013-06-12 19:57:00
updated: 2016-12-11 20:02:37
tags: 
- css
categories: 
- css

---
> 在面向对象的JS编程过程中，改变对象的this指向是很常见的，call和apply就是这个作用。就是把方法交给另外一个对象使用。在面向对象的继承方面，使用较多。

## 区别
区别就这么一句话:

```javascript
foo.call(this, arg1,arg2,arg3) == foo.apply(this, arguments)==this.foo(arg1, arg2, arg3)
```
`call`,`apply`都属于`Function.prototype`的一个方法,它是*JavaScript引擎*内在实现的,因为属于`Function.prototype`,所以**每个Function对象实例,也就是每个方法都有call, apply属性**.既然作为方法的属性,那它们的使用就当然是针对方法的了.这两个方法是容易混淆的,因为它们的作用一样,只是使用方式不同.不同点就是传递的参数不同。


<!--more-->


### call
`call` 方法可以用来代替另一个对象调用一个方法。call 方法可将一个函数的对象上下文从初始的上下文改变为由 `thisObj` 指定的新对象。如果没有提供 `thisObj` 参数，那么 `Globa`l 对象被用作 `thisObj`。

示例一(简单的替换this对象)
先来一个最基础的，就是替换this属性。

```javascript
// a class
function ClassOne() {
    this.name = "ClassOne";
    this.sayHi = function() {
        console.log(this.name);
    }
}
var classTest = new ClassOne();
var obj = new Object();
console.log(classTest.name); // "ClassOne"
classTest.sayHi(); // "ClassOne"
ClassOne.call(obj);
console.log(obj.name); // "ClassOne"
obj.sayHi(); // "ClassOne"
obj.name = "leohxj";
console.log(obj.name); // "leohxj"
obj.sayHi(); // "leohxj"
这样的一个例子，体现了call改变this的属性。现在obj也是一个ClassOne对象了。
```
### apply
作用同call，但是传递的参数不同。

*参考资料:*

 - https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FGlobal_Objects%2FFunction%2Fcall