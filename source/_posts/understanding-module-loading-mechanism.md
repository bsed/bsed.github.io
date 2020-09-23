---
title: 理解模块加载机制
date: 2014-06-22 20:43:00
updated: 2016-12-11 20:53:21
tags: 
- javascript
categories: 
- js

---
> JS规范目前主要有三类，CommonJS, AMD和CMD。

## CommonJS
`Node.js`就是CommonJS的一个部分实现。CommonJS是一个规范，要求你去实现它的定义。

CommonJS规范的加载是同步的，只有在加载完成之后，才能执行后面的操作，比较适合在服务器端，因为所有的文件都已经在硬盘之中，调用不存在延时。

根据CommonJS规范，一个单独的文件就是一个模块。加载模块使用require方法，该方法读取一个文件并执行，最后返回文件内部的exports对象。`Node.js`就是这么干的:


<!--more-->


```javascript
// example.js
console.log("evaluating example.js");
var invisible = function () {
  console.log("invisible");
}
exports.message = "hi";
exports.say = function () {
  console.log(this.message);
}
// loadModule.js
var example = require('./example.js');
console.log("example", example);
example.say();
```
这是两个文件，存放在同一个目录中。运行node loadModule.js显示：


D:\Workspace\node loadModule.js

```
evaluating example.js
example { message: 'hi', say: [Function] }
hi
```
## AMD
CommonJS提供了同步加载的方式，在浏览器环境中，更多的是需要异步加载。所以诞生了AMD的加载方式。模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

AMD是一种强依赖关系，所有依赖模块必须提前声明好，这样虽然对开发过程不算友好，但是性能上更优一些。使用方法主要是`require`和`define`。

define定义一个模块：

```javascript
define(['package/lib'], function(lib){
 
    function foo(){
        lib.log('hello world!');
    } 
 
    return {
        foo: foo
    };
});
```
AMD规范允许输出的模块兼容CommonJS规范，这时define方法需要写成下面这样：

```javascript
define(function (require, exports, module){
    var someModule = require("someModule");
    var anotherModule = require("anotherModule");
    someModule.doTehAwesome();
    anotherModule.doMoarAwesome();
    exports.asplode = function (){
        someModule.doTehAwesome();
        anotherModule.doMoarAwesome();
    };
});
```
require使用模块:
```javascript
require(['package/lib'], function(lib){
 
    function foo(){
        lib.log('hello world!');
    } 
 
    return {
        foo: foo
    };
});
```
## CMD
CMD更倾向在浏览器端使用，这是淘宝的玉伯发明的加载方式。和AMD的主要区别就是对依赖关系加载时序的处理。依赖不必开始就声明好，用到时再声明。



