---
title: "const、let和块级作用 理解 [JS基础增强]"
categories: [ "JS" ]
tags: [ "const","let","块级作用","JS基础增强" ]
draft: false
slug: "const-let-and-blocklevel-role-understanding-js-foundation-enhancement"
date: "2019-09-17 13:14:00"
---

> ES6提供了两种声明变量的方式：`let`和`const`, 它们主要用于取代ES5的变量声明方式：`var`。

## let

`let`和`var`的用法很相似，区别是`let`所声明的变量是**块级作用域**，它的作用域在当前块级({}内)区域里存在，`var`是函数作用域。


<!--more-->


在下面的代码里，可以看到`let`声明的变量`tmp`只存在于从行A开始的代码块内:

```Javascript
function foo(x, y) {
  if (x > y) { // (A)
    let tmp = x;
    x = y;
    y = tmp
  } // 作用域结束

  console.log(tmp === x) // ReferenceError: tmp is not defined
  return [x, y]
}
```

## const

`const`和`let`功能类似，区别是所声明的变量必须有初始值，并且这个变量一旦设定不能再次赋值。

```Javascript
const foo;
  // SyntaxError: missing = in const declaration

const bar = "kelvin";
bar = "zhao"
  // TypeError: invalid assignment to const `bar'
```

## `let`和`const`的块级作用域

`let`和`const`都可以**创建块级作用域**，下面代码展示`const`和`var`的区别：

```Javascript
function func() {
  if (true) {
    const tmp = "kelvin";
  }
  console.log(tmp); // ReferenceError: tmp is not defined
}

// 和const不同，var声明的变量是函数作用域
function func2() {
  if (true) {
    var tmp = "kelvin";
  }

  console.log(tmp); // kelvin
}
```

块级作用域意味着在函数内变量可以被覆盖 - `shadow`:

```Javascript
function func() {
  const foo = 5;
  if (true) {
    const foo = 10; // shadow外部的`foo`
    console.log(foo) // 10
  }
  console.log(foo); // 5
}
```

## `const`创建的变量不可变

通过`let`创建的变量可以改变（再次赋值）：

```Javascript
let foo = 'abc';
foo = 'def';
console.log(foo) // def
```

而通过`const`创建的变量是不可变的 -- 不能再次赋值:

```Javascript
const foo = 'abc'
foo = 'def' // TypeError
```

## 陷阱：`const`创建的变量的值并不是不能改变

`const`只是保证变量一次赋值，但是并不意味着值本身不可变。例如，`obj`是一个常量，但是它所指向的值可变：

```Javascript
const obj = {};
obj.prop = "kelvin";
console.log(obj.prop) // kelvin

// 但是，不能赋另外的值给`obj`:

obj = {}; // TypeError: invalid assignment to const `obj'
```

如果想要让`obj`的值也不可变，可以使用`freeze()`:

```Javascript
const obj = Object.freeze({})
obj.prop = 123; // TypeError
```

## 陷阱：object.freeze()是**shallow freeze**

记住`object.freeze()`是shallow freeze，什么是shallow freeze呢？shallow是**浅**的意思, 它只会freeze它的参数对象的属性，并不会freeze属性内的属性，例如：

```Javascript
const obj = Object.freeze({foo: {}})
obj.bar = 123 // TypeError: 不能添加属性bar, 对象不可扩展

obj.foo = {} // TypeError: 不能对只读属性'foo'赋值

// 但是`obj.foo`的值可以改变
obj.foo.qux = 'abc'
```

## `const`在循环体内

一旦`const`变量被创建后，它就不能再改变。但是这不意味着不能再次进入它的作用域，重新赋值，例如在一个循环里：

```Javascript
function logArgs(...args) {
  // 循环体里多次进入A作用域，message多次赋值
  for (const [index, elem] of args.entries()) { // (A)
    const message = index + '. ' + elem; // (B)
    console.log(message);
  }
}

logArgs('Hello', 'kelvin');

// 输出:
// 0. Hello
// 1. kelvin
```

## 临时死区 (The temporal dead zone)

用`let`或`const`声明的变量有一种被称作临时死区(TDZ)的现象，即: 当进入它们的作用域后，在执行定义`const`或者`let`的代码前，它们所定义的变量不能被访问（get或者set）。下面对比`let`和`var`.

```Javascript
let tmp = true;
if (true) { // 进入作用域, TDZ开始
  // 未初始化绑定的`tmp`被创建
  // 不能被访问
  console.log(tmp) // ReferenceError

  let tmp; // TDZ结束，
  console.log(tmp); // 可以访问，值为 undefined

  tmp = "kelvin";
  console.log(tmp); // kelvin
}
console.log(tmp) // true
```

下述代码表明TDZ确实是临时的（基于时间的），而非空间的:

```Javascript
if (true) { // 进入作用域, TDZ开始
  const func = function () {
    console.log(myVar); // 访问`myVar`成功
  };

  // 现在处于TDZ里,
  // 这时访问`myVar`会引起`ReferenceError`

  let myVar = 3; // TDZ 结束
  func(); // 在TDZ外调用函数
}
```

### 在TDZ里用`typeof`访问变量会抛`ReferenceError`

如果在TDZ里通过`typeof`访问一个变量，会抛得到一个exception:

```Javascript
if (true) {
  console.log(typeof foo); // 访问TDZ里的变量:ReferenceError (TDZ)
  console.log(typeof aVariableThatDoesntExist); // 访问一个不存在的变量:'undefined'
  let foo;
}
```

那么为什么会有以上结果呢？根本原因是：变量`foo`没有被声明，未被没有初始化; 这种检查只适用于根据条件创建全局变量

## 根据条件创建全局变量

根据条件创建全局变量有两种方式：

- 方式-：使用`typeof`和`var`:

```Javascript
if (typeof someGlobal === 'undefined') {
  var someGlobal = { ··· };
}
```

- 方式二: 使用`window`:

```Javascript
if (!('someGlobal' in window)) {
  window.someGlobal = { ··· };
}
```

来源：网络，出处已无法考证。