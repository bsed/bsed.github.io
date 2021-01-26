---
title: "ES7特性预览"
categories: [ "JS" ]
tags: [ "js","ecmascript7" ]
draft: false
slug: "es7-feature-preview"
date: "2016-03-05 20:48:00"
---

> ES6都还没得及，现在一些ES7的新特性就出来了。得益于Node/Native这种玩意将JavaScript发扬光大，所以很多特性是并不是针对浏览器脚本设计的，很多针对Server端，或者本地应用。所以浏览器也不会支付所以有ES6/ES7的规范。

## ES7特性预览

当然，我还是得在这里澄清一下，ES6规范已经在年中已经正式发布了，发布之后的名字前不是 ECMAScript 6，而是 `ECMAScript 2015`，本文的标题 ES7 目前的版本是 `ECMAScript 2016`，正式发布之后叫什么就不知道了，本人对名字什么的不纠结，怎么顺口就怎么叫。即使你叫它`ES6`，`ES7`也没什么错，它就是ECMAScript第6版，第7版。
废话就这么多，直接进入正题了。

## 乘方运算符

乘方运算较简单。目前一些语言里面其实以经有了。代码一看就明白：

```javascript
let squared = 2 ** 2; //  = 2 * 2 = 4
let cubed = 2 ** 3; // = 2 * 2 * 2 = 8

let x = 2;
x **= 3; // x = x**3 = x * x * x = 8;
```
## Array.prototype.includes

这个也好理解，之前一般使用indexOf这个函数的结果是否大于0去判定一个数组里面是否存在一个元素。现在使用includes可以直接返回。相对于indexOf的性能，具体算法上有没有优化只能看各种平台的实现了。

```javascript
//旧版本实现
if (arr.indexOf(element) >= 0) {
	alert('include element');
}
//新版本实现
if (arr.includes(element)){
	alert('include element');
}
```
和`indexOf`一样，支付第二个传数传入起始位置，如：

```
/旧版本实现,从第5个数开始查找
if (arr.indexOf(element, 5) >= 0) {
	alert('include element');
}
//新版本实现,从第5个数开始查找
if (arr.includes(element, 5)){
	alert('include element');
}
```

## 异步方法 & function.sent

最近无论什么语言，都想在并发方面做的更好，ES当然也一样，ES6前，像jQuery，CommonJS ，requireJs等都根据Promise规范自己实现一套Promise（Promise规范分多种，可能依照的规范不同，不多说明），在ES6中定义了规范化的Promise对象（[基于Promises A](https://promisesaplus.com/)），在ES7中，增加了异步方法更好的进行并发。

```javascript
async function asyncCall(elem, animations){
  let ret = null;
        try {
            for(const anim of animations) {
                ret = await anim(elem);
            }
        } catch(e) {

		}
        return ret;
}
```
编辑器还没有反应过来,上面的`async`， `await`都是ES7新增的关键字，定义方法前加上async关键字代表方法是异步，与`await`同级的还有`yield,`可以返回一个自增的数据，**await表示等待**。
对于异步方法，好像很有趣的样子，但由于本人较少接触服务端开发，基本不会用到，所以就没细看了，想了解的可以去传送门详细去了解！
对于 function.sent属性

## [Object.observe](http://arv.github.io/ecmascript-object-observe/)

从字面上来看，是设计模式里面的观察者模式，从代码编写上来看，是面向切面编程的写法，从功能上来看，最大的做用就是实现AngularJs类似的数据绑定，当然还可以实现其它的。
其它ES6的代理也可以有类似的功能，但不方便，功能也没observe强大。使用`Object.observe/Object.deliverChangeRecords`开启

```javascript
//定义数据及切面方法
var records;
function observer(recs) {
  records = recs;
}
var obj = { id: 1 };
//将对象“观察”
Object.observe(obj, observer);
obj.a = 'b';
obj.id++;
//删除属性 a
Object.defineProperty(obj, 'a', { enumerable: false });
delete obj.a;
//禁用扩展
Object.preventExtensions(obj);
//开启对象观察（切面）
Object.deliverChangeRecords(observer);
//单元测试
assertChangesAre(records, [
  { object: obj, type: 'add', name: 'a' },
  { object: obj, type: 'update', name: 'id', oldValue: 1 },
  { object: obj, type: 'reconfigure', name: 'a' },
  { object: obj, type: 'delete', name: 'a', oldValue: 'b' },
  { object: obj, type: 'preventExtensions', }
]);
```
## Object.values / Object.entries

直接上代码，不难理解
```javascript
var assert = require('assert');
var values = require('object.values');
var entries = require('object.entries');

var obj = { a : 1, b : 2, c : 3 };
var expected  = { ['a', 1], ['b', 2], ['c', 3] };

var sym = Symbol();
obj[sym] = 4;
obj.d = sym;
expected.push(['d', sym]);

if (!Object.values) {
    values.shim();
}
if (!Object.entries) {
    entries.shim();
}

assert.deepEqual(Object.values(obj), expected);
assert.deepEqual(Object.entries(obj), expected);
```

## 语法修复：支持方法参数里面以逗号结尾

这个目前已经有许多JavaScript的实现，支持Json格式，在ES7里面可以应用到方法参数里面：
//比如,有些人喜欢这样写代码：
```javascript
function method(
	a1,
	a2
	){
		//TODO
	}
method(
	1,
	2
);

//那么，当方法method扩展了一个参数时，就可以做这样修改
function method(
	a1,
	a2,	//多加一个逗号
	a3	 //加入新的参数，不带逗号
	){
		//TODO
	}
method(
	1,
	2,	//多加一个逗号
	3 	//加入新的参数，不带逗号
);

//在ES7里面可以这样了
function method(
	a1,
	a2,	//不管怎么样你都可以带个逗号，而不用担心语法错误，
	){
		//TODO
	}
method(
	1,
	2,	//当进行参数扩展的时候，直接加入新的扩展参数即可,加入的新的扩展参数也可以带逗号结尾
);
```
## 数据类型

谁都知道JavaScript是一个弱类型语言，基本上用  `typeof` 方法只可能出现6种结果，（ES6后又多了symbol）,如下：

- undefined
- string
- number
- object
- function
- boolean
- symbol

其实到了ES7之后也还是这么几种-_-!!， 只不过对于object对象有了一些新的定义。对一些针对性的object的数据结构进一些优化，降低内在占用，提高性能。
主要是内部实现上，优化了以下类型：

- Structure: (其中之一) uint8, int8, uint16, int16, uint32, int32, float32, float64, any, string, object
- FieldRecord : (数据字段) { name:属性名, `type:TypeDescriptor ：类型描述` }
- Dimensions: (内存空间) 空，或者根据自身数据类型计算。 比如 int8类型就只有1字节

TypeDescriptor 类型描述：

- Structure： (其中之一) uint8, int8, uint16, int16, uint32, int32, float32, float64, any, string, object
- Rank：对于简单数据类型都是0，Array类型，就是当前array的深度
- ArrayDescriptor： 如果是 Array数据，那么这个值又是一个 TypeDescriptor 非数组，就是 undefined
所以，一个对象所点用的内存空间就可以为 `length(Dimensions) = rank of TypeDescriptor`

当然，具体实现还要看厂商的了，我们貌似不用关心这些问题。有了数据类型后，会多出一些方法来，还有构造器，由于不常用且没有实例，就看着方法名YY吧，问我我也不知道：

- AlignTo(value, alignment)
- IsTypeObject(O)
- Alignment(typeDescriptor)
- Size(typeObject)
- Size(structure, dimensions)
- Size(structure)
- OffsetOf(fieldRecords, name?)
- Opaque(structure)
- CreateStructTypeDescriptor(structure)
- CreateArrayTypeDescriptor(typeDescriptor)
- GetOrCreateArrayTypeDescriptor(typeDescriptor)
- CreateTypedObjectFromBuffer(arrayBuffer, byteOffset, typeObject)
- CreateTypedObject(typeObject)
- Default(typeDescriptor)
- Coerce(typeDescriptor, value)
- Initialize(typeDescriptor, dimensions, buffer, offset)
- ConvertAndCopyTo(typeDescriptor, dimensions, buffer, offset, value)
- Reify(typeDescriptor, dimensions, buffer, offset, opacity)

## ArrayBuffer.transfer

貌似会增加一个ArrayBuffer类。方便处理理数流，

```javascript
ar buf1 = new ArrayBuffer(40);
new Int32Array(buf1)[0] = 42;

var buf2 = ArrayBuffer.transfer(buf1, 80);
assert(buf1.byteLength == 0);
assert(buf2.byteLength == 80);
assert(new Int32Array(buf2)[0] == 42);

var buf3 = ArrayBuffer.transfer(buf2, 0);
assert(buf2.byteLength == 0);
assert(buf3.byteLength == 0);
```

## 新的模块导出声明

ES6里面规范了模块的导入导出模式，（非CommonJS和AMD），语法如
目前ES6定义的导入和导出语法如下:

**导入:**

| 语法 | 导入请求的模块 | 导入模块里面的变量 | 本模块对些变量的命名 |
|--------|--------| --------- | ------------- |
| import v from "mod";  | "mod"	| "default"	| "v" |
| mport * as ns from "mod";  | 	"mod"  | "*"  | "ns"  |
| import {x} from "mod"; | "mod"  | "x"  | 	"x"  |
| import {x as v} from "mod";  | 	"mod"	  | "x"  | 	"v"  |
| import "mod";  |  |    |     |  

**导出:**


| 语法  | 导入的模块 | 导入的模块里面的变量 | 本地变量 | 出对外的变量名 |
|-------- |--------|  -------| ------------ |  ------  |
| export var v; | null	null	"v"	"v"
| export default function f(){}; | 	null | null	 | "f"	 | "default" |
| export default function(){}; | 	null | 	nul | l	"*default*" | 	"default" |
| export default 42; | 	null | 	null | 	"*default*" | 	"default" |
| export {x}; | null | 	null | 	"x" | 	"x" |
| export {x as v}; |  null | 	null	 | "x" | 	"v" |
| export {x} from "mod"; | 	"mod" | 	"x"	 | null | 	"x" |
| export {x as v} from "mod"; | "mod"	 | "x"	 | null | 	"v" |
| export * from "mod";	 | "mod" | 	"*" | 	null | 	null

## ES7新增语法

| 语法 | 导入的模块 | 导入的模块里面的变量 | 本地变量 | 导出对外的变量名 |
|-------- |--------|  -------| ------------ |  ------  |
| export * as ns from "mod"; | 	"mod"	 | "*"	 | null | "ns" |
| export v from "mod"; | 	"mod"  | "default" | 	null | 	"v" |

主要是增加了从另外一个模块导出，对于代理方法可能会有所简化。

## Class 和 Property 使用Decorators(修饰运算)

这个可能看不太懂。什么是修饰运算？，可以为以下几种，不知道用中文怎么表达，直接贴原文了。
1. an expression
2. that evaluates to a function
3. that takes the target, name, and property descriptor as arguments
4. and optionally returns a property descriptor to install on the target object

比如定义一个属性：

```javascript
class Person {
  @readonly
  name() { return `${this.first} ${this.last}` }
}
```language
```

定义一个类：

```javascript
//在ES6之前
var Foo = (function () {
  function Foo() {
  }
  Foo = F("color")(Foo = G(Foo) || Foo) || Foo;
  return Foo;
})();

//ES6
var Foo = (function () {
  class Foo {
  }

  Foo = F("color")(Foo = G(Foo) || Foo) || Foo;
  return Foo;
})();

//ES7
@F("color")
@G
class Foo {
}
```

## String 字符串相关

规范了`String.prototype.trimLeft / trimRight `方法。
这个不用说了，很多厂商自己已经实现了这两个方法。(比如Firefox)
新增加`padLeft/padRight`
[传送门](http://tc39.github.io/proposal-string-pad-left-right/)，把字符串向左隔开，或向右隔开:

- String.prototype.padLeft( maxLength [ , fillString ] )
- String.prototype.padRight( maxLength [ , fillString ] )

maxLength为最大长度，也是最终长度（当maxLength大于字符串本身长度时），往左或往右填入补充字符串，默认为U+0020(即空格：SPACE)

## 正则表达式新方法matchAll

`String.prototype.matchAll` 匹配全部符合要求的项。

```javascript
var regex = /t(e)(st(\d?))/g;
var string = 'test1test2';
string.matchAll(regex); //返回一个遍历对象，类似数组，有[keys / values / entries]信息
```
