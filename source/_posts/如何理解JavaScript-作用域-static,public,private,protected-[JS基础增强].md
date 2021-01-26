---
title: "如何理解JavaScript 作用域 static,public,private,protected [JS基础增强]"
categories: [ "JS" ]
tags: [ "javascript","作用域" ]
draft: false
slug: "how-to-understand-the-scope-of-ja-x-vasc-x-rip-static-public-private-protected"
date: "2017-11-13 11:22:00"
---

本文主要带你理解JS类中的**static**,**public**,**private**,**protected**这些关键字的使用。

## **static**

Javascript类（class）通过 关键字**static** 定义静态方法。我们不能在类的实例上调用静态方法，而应该通过类本身调用。（这种调用跟许多编程语言比较类似，如Java、Php等）。

### 语法


<!--more-->


```
`static methodName() { ... }`
```

### 描述

静态方法(static)调用直接在类上进行，不能在类的实例上调用。

### 调用静态方法

#### 从另一个静态方法中

静态方法可以调用同一个类中的其他静态方法，可使用**this**关键字。

```javascript
class People {
  static staticMethod() {
    return "Static method has been called";
  }
  static anotherStaticMethod() {
    return this.staticMethod() + " from another static method";
  }
}
People.staticMethod();
// 'Static method has been called'

People.anotherStaticMethod();
// 'Static method has been called from another static method'

```

#### 从类的构造函数和其他方法

非静态方法中，不能直接使用 this关键字来访问静态方法。而是要用类名来调用：`CLASSNAME.STATIC_METHOD_NAME()` ，或者用**构造函数的属性**来调用该方法： `this.constructor.STATIC_METHOD_NAME()`.

```
class People {
    constructor() {
        console.log(People.staticMethod());
        // 'static method has been called.'
        console.log(this.constructor.staticMethod());
        // 'static method has been called.'
    }
    static staticMethod() {
        return 'static method has been called.';
    }
}
```

## public

对象的成员都是public成员。任何对象都可以访问，添加，修改，删除这些成员。主要有两种方式来在一个新对象里放置成员:

### 在构造函数里

这种技术通常用来初始化public实例变量。构造函数的“this”变量用来给对象添加成员。

```
`functin People(param) {  this.member = param;  }`
```

这样，如果我们构造一个新对象var people = new People(‘kelvin,mbioq’)，people.member为’kelvin,mbioq’。

**在prototype里**
这种通常用来添加public方法。当寻找一个成员并且它不在对象本身里时，则从对象的构造函数的prototype成员里找。
**prototype机制用来做继承**。为了添加一个方法到构造函数创建的所有对象里，只需添加到构造函数的prototype:

```
`People.prototype.eat = function (string) {  return this.member + string;  }`
```

这样，我们可以调用该方法myContainer.eat(‘egg’)，结果为’kelvin,mbioqegg’。

## private

private成员由构造函数产生。普通的var变量和构造函数的参数都称为private成员。

```javascript
function People(param) 
	{  
		this.member = param;  
		var secret = 401;  
		var that = this;  
 }
```

该构造函数创建了3个private实例变量: `param`，`secret`和`that`。它们被添加到对象中，但是不能被外部访问，也不能被该对象自己的 public方法访问。它们只能由private方法访问。private方法是构造函数的内部方法。

```javascript
function People () {
	this.name = "kelvin";
	var age = 18;
	this.getName = function () {
		return this.name
	};
	this.getAge = function () {
		return age;
	};
}

var kelvin = new People();
// undefined
console.info(kelvin.age);
// 18
console.info(kelvin.getAge())
```

## protected

protected可以修饰成员属性，成员方法，构造方法，不能修饰类（此处指外 部类，不考虑内部类）。被protected修饰的成员，能在定义它们的类中，同包的类中被调用。如果有不同包的类想调用它们，那么这个类必须是定义它们的类的子类。

```
//module foo:
class Foo {
    constructor() {
        this[Foo.PROPERTY] = 'hello';
    }
    test() {
      console.log(this[Foo.PROPERTY]);
    }
}
Foo.PROPERTY = Symbol();
export default Foo;

//module bar:
import Foo from '(module foo)';
class Bar extends Foo {
    test2() {
      console.log(this[Bar.PROPERTY]);
    }
}
export default Bar;

//module main:
import Bar from '(module bar)';
new Bar.test2();
```

如下面表格：

| 关键字    | 类本身 | 类的方法 | 类的实例 | 子类 | 子类方法 | 子类的实例 |
| --------- | ------ | -------- | -------- | ---- | -------- | :--------: |
| static    | +      | –        | –        | +    | –        |     –      |
| public    | –      | +        | +        | –    | +        |     +      |
| private   | –      | +        | –        | –    | –        |     –      |
| protected | –      | +        | –        | –    | +        |     –      |