---
title: ECMAScript6教程 解构 Destructuring
date: 2015-12-18 09:08:00
updated: 2016-01-24 09:18:14
tags: 
- ecmascript6
- class
categories: 
- js

---
## 什么是解构赋值？

解构赋值允许你使用类似数组或对象字面量的语法将数组和对象的属性赋给各种变量。这种赋值语法极度简洁，同时还比传统的属性访问方法更为清晰。

通常来说，你很可能这样访问数组中的前三个元素：
```javascript
    var first = someArray[0];
    var second = someArray[1];
    var third = someArray[2];
```
如果使用解构赋值的特性，将会使等效的代码变得更加简洁并且可读性更高：

```javascript
    var [first, second, third] = someArray;
```
SpiderMonkey（Firefox的JavaScript引擎）已经支持解构的大部分功能，但是仍不健全。你可以通过[bug 694100](https://bugzilla.mozilla.org/show_bug.cgi?id=694100)跟踪解构和其它ES6特性在SpiderMonkey中的支持情况。

## 数组与迭代器的解构

以上是数组解构赋值的一个简单示例，其语法的一般形式为：

```javascript
[ variable1, variable2, ..., variableN ] = array;
```
这将为variable1到variableN的变量赋予数组中相应元素项的值。如果你想在赋值的同时声明变量，可在赋值语句前加入`var`、`let`或`const`关键字，例如：

```javascript
     var [ variable1, variable2, ..., variableN ] = array;
     let [ variable1, variable2, ..., variableN ] = array;
     const [ variable1, variable2, ..., variableN ] = array;
```
事实上，用变量来描述并不恰当，因为你可以对任意深度的嵌套数组进行解构：

```javascript
    var [foo, [[bar], baz]] = [1, [[2], 3]];
    console.log(foo);
    // 1
    console.log(bar);
    // 2
    console.log(baz);
    // 3
```
此外，你可以在对应位留空来跳过被解构数组中的某些元素：


<!--more-->


```javascript
    var [,,third] = ["foo", "bar", "baz"];
    console.log(third);
    // "baz"
```
而且你还可以通过“不定参数”模式捕获数组中的所有尾随元素：

```javascript
    var [head, ...tail] = [1, 2, 3, 4];
    console.log(tail);
    // [2, 3, 4]
```
当访问空数组或越界访问数组时，对其解构与对其索引的行为一致，最终得到的结果都是：undefined。

```javascript
    console.log([][0]);
    // undefined
    var [missing] = [];
    console.log(missing);
    // undefined
```

请注意，数组解构赋值的模式同样适用于任意迭代器：

```javascript
 function* fibs() {
      var a = 0;
      var b = 1;
      while (true) {
        yield a;
        [a, b] = [b, a + b];
      }
    }
    var [first, second, third, fourth, fifth, sixth] = fibs();
    console.log(sixth);
    // 5
```

## 对象的解构

通过解构对象，你可以把它的每个属性与不同的变量绑定，首先指定被绑定的属性，然后紧跟一个要解构的变量。
```javascript
    var robotA = { name: "Bender" };
    var robotB = { name: "Flexo" };
    var { name: nameA } = robotA;
    var { name: nameB } = robotB;
    console.log(nameA);
    // "Bender"
    console.log(nameB);
    // "Flexo"
```
当属性名与变量名一致时，可以通过一种实用的句法简写：

```javascript
    var { foo, bar } = { foo: "lorem", bar: "ipsum" };
    console.log(foo);
    // "lorem"
    console.log(bar);
    // "ipsum"
```
与数组解构一样，你可以随意嵌套并进一步组合对象解构：
```javascript
    var complicatedObj = {
      arrayProp: [
        "Zapp",
        { second: "Brannigan" }
      ]
    };
    var { arrayProp: [first, { second }] } = complicatedObj;
    console.log(first);
    // "Zapp"
    console.log(second);
    // "Brannigan"
```
当你解构一个未定义的属性时，得到的值为undefined：

```javascript
    var { missing } = {};
        console.log(missing);
        // undefined
```
请注意，当你解构对象并赋值给变量时，如果你已经声明或不打算声明这些变量（亦即赋值语句前没有let、const或var关键字），你应该注意这样一个潜在的语法错误：
```javascript
    { blowUp } = { blowUp: 10 };
    // Syntax error 语法错误
```
为什么会出错？这是因为JavaScript语法通知解析引擎将任何以{开始的语句解析为一个块语句（例如，{console}是一个合法块语句）。解决方案是将整个表达式用一对小括号包裹：
```javascript
    ({ safe } = {});
    // No errors 没有语法错误
```
## 解构值不是对象、数组或迭代器

当你尝试解构null或undefined时，你会得到一个类型错误：
```javascript
    var {blowUp} = null;
    // TypeError: null has no properties（null没有属性）
```
然而，你可以解构其它原始类型，例如：布尔值、数值、字符串，但是你将得到undefined：

```javascript
    var {wtf} = NaN;
    console.log(wtf);
    // undefined
```

你可能对此感到意外，但经过进一步审查你就会发现，原因其实非常简单。当使用对象赋值模式时，被解构的值需要被[强制转换为对象](https://people.mozilla.org/~jorendorff/es6-draft.html#sec-requireobjectcoercible)。大多数类型都可以被转换为对象，但null和undefined却无法进行转换。当使用数组赋值模式时，被解构的值一定要[包含一个迭代器](https://people.mozilla.org/~jorendorff/es6-draft.html#sec-getiterator)。

## 默认值

当你要解构的属性未定义时你可以提供一个默认值：
```javascript
    var [missing = true] = [];
    console.log(missing);
    // true
    var { message: msg = "Something went wrong" } = {};
    console.log(msg);
    // "Something went wrong"
    var { x = 3 } = {};
    console.log(x);
    // 3
```
(译者按：Firefox目前只实现了这个特性的前两种情况，第三种尚未实现。详情查看[bug 932080](https://bugzilla.mozilla.org/show_bug.cgi?id=932080)。)

## 解构的实际应用
### 函数参数定义

作为开发者，我们需要实现设计良好的API，通常的做法是为函数为函数设计一个对象作为参数，然后将不同的实际参数作为对象属性，以避免让API使用者记住 多个参数的使用顺序。我们可以使用解构特性来避免这种问题，当我们想要引用它的其中一个属性时，大可不必反复使用这种单一参数对象。
```javascript
    function removeBreakpoint({ url, line, column }) {
      // ...
    }
```
这是一段来自Firefox开发工具JavaScript调试器（同样使用JavaScript实现——没错，就是这样！）的代码片段，它看起来非常简洁，我们会发现这种代码模式特别讨喜。

## 配置对象参数

延伸一下之前的示例，我们同样可以给需要解构的对象属性赋予默认值。当我们构造一个提供配置的对象，并且需要这个对象的属性携带默认值时，解构特性就派上用场了。举个例子，jQuery的ajax函数使用一个配置对象作为它的第二参数，我们可以这样重写函数定义：
```javascript
    jQuery.ajax = function (url, {
      async = true,
      beforeSend = noop,
      cache = true,
      complete = noop,
      crossDomain = false,
      global = true,
      // ... 更多配置
    }) {
      // ... do stuff
    };
```
如此一来，我们可以避免对配置对象的每个属性都重复var foo = config.foo || theDefaultFoo;这样的操作。

（编者按：不幸的是，对象的默认值简写语法仍未在Firefox中实现，我知道，上一个编者按后的几个段落讲解的就是这个特性。点击[bug 932080](https://bugzilla.mozilla.org/show_bug.cgi?id=932080)查看最新详情。）

## 与ES6迭代器协议协同使用

ECMAScript 6中定义了一个迭代器协议。当你迭代Maps（ES6标准库中新加入的一种对象）后，你可以得到一系列形如[key, value]的键值对，我们可将这些键值对解构，更轻松地访问键和值：
```javascript
    var map = new Map();
    map.set(window, "the global");
    map.set(document, "the document");
    for (var [key, value] of map) {
      console.log(key + " is " + value);
    }
    // "[object Window] is the global"
    // "[object HTMLDocument] is the document"
```
只遍历键：
```javascript
    for (var [key] of map) {
      // ...
    }
```
或只遍历值：
```javascript
    for (var [,value] of map) {
      // ...
    }
```

## 多重返回值

JavaScript语言中尚未整合多重返回值的特性，但是无须多此一举，因为你自己就可以返回一个数组并将结果解构：
```javascript
    function returnMultipleValues() {
      return [1, 2];
    }
    var [foo, bar] = returnMultipleValues();
```
或者，你可以用一个对象作为容器并为返回值命名：
```javascript
    function returnMultipleValues() {
      return {
        foo: 1,
        bar: 2
      };
    }
    var { foo, bar } = returnMultipleValues();
```
这两个模式都比额外保存一个临时变量要好得多。
```javascript
    function returnMultipleValues() {
      return {
        foo: 1,
        bar: 2
      };
    }
    var temp = returnMultipleValues();
    var foo = temp.foo;
    var bar = temp.bar;
```
或者使用CPS变换：
```javascript
    function returnMultipleValues(k) {
      k(1, 2);
    }
    returnMultipleValues((foo, bar) => ...);
```
使用解构导入部分CommonJS模块

你是否尚未使用ES6模块？还用着CommonJS的模块呢吧！没问题，当我们导入CommonJS模块X时，很可能在模块X中导出了许多你根本没打算用的函数。通过解构，你可以显式定义模块的一部分来拆分使用，同时还不会污染你的命名空间：

```javascript
 const { SourceMapConsumer, SourceNode } = require("source-map");
```
（如果你使用ES6模块，你一定知道在import声明中有一个相似的语法。）

## 文后盘点

所以，正如你所见，解构在许多独立小场景中非常实用。在Mozilla我们已经积累了许多有关解构的使用经验。十年前，Lars Hansen在Opera中引入了JS解构特性，Brendan Eich随后就给Firefox也增加了相应的支持，移植时版本为Firefox 2。所以我们可以肯定，渐渐地，你会在每天使用的语言中加入解构这个新特性，它可以让你的代码变得更加精简整洁。
