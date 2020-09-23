---
title: JavaScript：ES2019 的新特性速览 [JS基层增强]
date: 2019-08-22 13:50:00
updated: 2019-08-22 14:26:30
tags: 
- ssr
categories: 
- default

---
JavaScript 不断改进和添加更多功能。TC39 已经完成并批准了 ES2019 的这 8 个功能，它有 4 个阶段，这些阶段是：

- Stage 0: Strawman
- Stage 1: Proposals
- Stage 2: Drafts
- Stage 3: Candidates
- Stage 4: Finished/Approved

以下链接可以查看[Stage 0](https://github.com/tc39/proposals/blob/master/stage-0-proposals.md)，[Stage 1 – 3 ](https://github.com/tc39/proposals)和[Final Stage](https://github.com/tc39/proposals/blob/master/finished-proposals.md)

下面让我们来看看 ES2019 有哪些新的特性，并可以在将来加入到我们日常开发中:
![ecma-moddable_mbioq.png][1]
## Array.prototype.flat()

`Array.prototype.flat()` 递归地将嵌套数组拼合到指定深度。默认值为 1，如果要全深度则使用 **Infinity** 。此方法不会修改原始数组，但会创建一个新数组:


<!--more-->


```javascript
const arr1 = [1, 2, [3, 4]];
arr1.flat(); // [1, 2, 3, 4]

const arr2 = [1, 2, [3, 4, [5, 6]]];
arr2.flat(2); // [1, 2, 3, 4, 5, 6]

const arr3 = [1, 2, [3, 4, [5, 6, [7, 8]]]];
arr3.flat(Infinity); // [1, 2, 3, 4, 5, 6, 7, 8]
```

`flat()` 方法会移除数组中的空项:

```javascript
const arr4 = [1, 2, , 4, 5];
arr4.flat(); // [1, 2, 4, 5]
```

## Array.prototype.flatMap()

`flatMap()` 方法首先使用映射函数映射每个元素，然后将结果压缩成一个新数组。它与 `Array.prototype.map` 和 深度值为 1的 `Array.prototype.flat` 几乎相同，但 `flatMap` 通常在合并成一种方法的效率稍微高一些。

```javascript
const arr1 = [1, 2, 3];

arr1.map(x => [x * 4]); // [[4], [8], [12]]
arr1.flatMap(x => [x * 4]); // [4, 8, 12]
```

示例：

```javascript
const sentence = ["This is a", "regular", "sentence"];

sentence.map(x => x.split(" ")); // [["This","is","a"],["regular"],["sentence"]]
sentence.flatMap(x => x.split(" ")); // ["This","is","a","regular", "sentence"]
// 可以使用 归纳（reduce） 与 合并（concat）实现相同的功能 sentence.reduce((acc, x) => acc.concat(x.split(" ")), []);
```

## String.prototype.trimStart() 和 String.prototype.trimEnd()

除了能从字符串两端删除空白字符的 `String.prototype.trim()` 之外，现在还有单独的方法，只能从每一端删除空格:

```javascript
const test = " hello ";

test.trim(); // "hello";
test.trimStart(); // "hello ";
test.trimEnd(); // " hello";
```

- `trimStart()` ：别名 `trimLeft()`，移除原字符串左端的连续空白符并返回，并不会直接修改原字符串本身。

- `trimEnd()` ：别名 `trimRight()`，移除原字符串右端的连续空白符并返回，并不会直接修改原字符串本身。

  

## Object.fromEntries

将键值对列表转换为 Object 的新方法。

 - Object.entries是将一个键值对对象，转换为Array
 - Object.fromEntries是将Array或Map等对象，转换为键值对对象

它与已有 **Object.entries()** 正好相反，`Object.entries()`方法在将对象转换为数组时使用，它返回一个给定对象自身可枚举属性的键值对数组。

但现在您可以通过 `Object.fromEntries` 将操作的数组返回到对象中。

下面是一个示例（将所有对象属性的值平方）:

```javascript
const obj = { prop1: 2, prop2: 10, prop3: 15 };

let array = Object.entries(obj); // [["prop1", 2], ["prop2", 10], ["prop3", 15]]
```

将所有对象属性的值平方:

```javascript
array = array.map(([key, value]) => [key, Math.pow(value, 2)]); // [["prop1", 4], ["prop2", 100], ["prop3", 225]]
```

我们将转换后的数组 `array` 作为参数传入 **Object.fromEntries** ，将数组转换成了一个对象:

```javascript
const newObj = Object.fromEntries(array); // {prop1: 4, prop2: 100, prop3: 225}

```

## 可选的 Catch 参数 (Catch Binding)

新提案允许您完全省略 `catch()` 参数，调整了try catch的使用方法，现在当Catch Binding没有使用的时候，可以移除。

之前是这样写的:
```javascript
try {
  //...
} catch (exception) {
  //handle error with parameter er
}
```

现在没有使用exception时，直接可以忽略不写
```javascript
try {
  //...
} catch {
  //handle error without parameter
}
```
## Symbol.description

`description` 是一个只读属性，它会返回 `Symbol` 对象的可选描述的字符串，用来代替 `toString()` 方法。

Symbol 是在ES2015中非常独特的功能
在ES2019中，它现在可以直接使用description，其目标是避免间接的从`Symbol.prototype.toString获得所提供的Description

```javascript
const mySymbol = Symbol('Desc');
 
console.log(mySymbol); // Symbol(Desc)
 
console.log(mySymbol.toString()); // Symbol(Desc)
 
console.log(mySymbol.description); // Desc
```

## Function.toString()

在ES2019之前，JS原型就已经存在toString方法，这次主要是做了一些小的调整，toString现在输出的内容包括空格和注释。

```javascript
function /* kelvin*/ foo /*mbioq*/ (){}
 
// Before
console.log(foo.toString()); // function foo(){}
 
// Now ES2019
console.log(foo.toString()); // function /* kelvin*/ foo /*mbioq*/ (){}
 
 
// Arrow Syntax
const bar /* kelvin*/ = /*mbioq*/ () => {}
 
console.log(bar.toString()); // () => {}
```
**注意**：从上方的console输出可以看出，箭头函数的注释内容，是不会输出的。

## JSON.parse() 改进

行分隔符 **(\u2028)** 和段落分隔符 **(\u2029)**，现在被正确解析，而不是报一个语法错误。
在ES2019之前，转义这两个字符会报错 `SyntaxError: Invalid or unexpected token.`

```javascript
var str = '{"name":"Bottle\uAnGe"}'
JSON.parse(str) // {name: "BottleAnGe"}
```

## Well-formed JSON.stringify

ES2019不是将未配对的代理代码点作为单个UTF-16代码单元返回，而是用JSON转义序列表示它们。
```javascript
// Before
console.log(JSON.stringify('\uD800')); // "�"
 
// Now ES2019
console.log(JSON.stringify('\uD800')); // "\ud800"
```

**其他**

强调一下现在 Stage 3 中的一些有用的即将推出的功能。

- [globalThis](https://github.com/tc39/proposal-global)
- [BigInt](https://github.com/tc39/proposal-bigint)
- [import()](https://github.com/tc39/proposal-dynamic-import)
- [Legacy RegEx](https://github.com/tc39/proposal-regexp-legacy-features)
- [Private instance methods and accessors](https://github.com/tc39/proposal-private-methods)
- [String.prototype.matchAll](https://github.com/tc39/proposal-string-matchall)

参考链接：[https://blog.tildeloop.com/posts/javascript-what’s-new-in-es2019](https://blog.tildeloop.com/posts/javascript-what’s-new-in-es2019)


  [1]: https://imgs.gnux.cn/usr/uploads/2019/08/1127211719.png