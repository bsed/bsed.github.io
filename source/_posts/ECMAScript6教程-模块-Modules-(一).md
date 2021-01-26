---
title: "ECMAScript6教程 模块 Modules (一)"
categories: [ "JS" ]
tags: [ "ecmascript6","modules" ]
draft: false
slug: "ecmascript6-tutorial-module-modules"
date: "2015-12-27 16:47:00"
---

## 模块基础知识
每一个ES6模块都是一个包含JS代码的文件，模块本质上就是一段脚本，而不是用module关键字定义一个模块，但是模块与脚本还是有两点区别：

在ES6模块中，无论你是否加入“use strict;”语句，默认情况下模块都是在严格模式下运行。
在模块中你可以使用import和export关键字。
我们先来讨论export。默认情况下，你在模块中的所有声明相对于模块而言都是寄存在本地的。如果你希望公开在模块中声明的内容，并让其它模块加以使用，你一定要导出这些功能。想要导出模块的功能有很多方法，其中最简单的方式是添加export关键字。
```javascript
    // kittydar.js - 找到一幅图像中所有猫的位置
    // （事实上是Heather Arthur写的这个库）
    // （但是她没有使用ES6中新的模块特性，因为那时候是2013年）
    export function detectCats(canvas, options) {
      var kittydar = new Kittydar(options);
      return kittydar.detectCats(canvas);
    }
    export class Kittydar {
      ... 处理图片的几种方法 ...
    }
    // 这个helper函数没有被export。
    function resizeCanvas() {
      ...
    }
    ...
```
你可以导出所有的最外层函数、类以及var、let或const声明的变量。


<!--more-->


了解这些，你就可以编写一个简单的模块。你不需要将所有代码都放在一个[IIFE](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression)或回调中，你只需要在模块中解放手脚，声明你需要的所有内容。代码就是模块，不是一段脚本，所以所有的声明都被限定在模块的作用域中，对所有脚本和模块全局不可见。你需要做的是将组成模块公共API的声明全部导出。

在模块中，除export之外的代码无异于普通代码，你可以访问类似Object和Array这样的全局对象。如果你在web浏览器中运行模块，你甚至可以使用document对象和XMLHttpRequest对象。

在一个独立文件中，我们可以导入detectCats()函数然后用它来做点儿什么：
```javascript
    // demo.js - Kittydar的demo程序
    import {detectCats} from "kittydar.js";
    function go() {
        var canvas = document.getElementById("catpix");
        var cats = detectCats(canvas);
        drawRectangles(canvas, cats);
    }
```
如果想从一个模块中导入多个名称，你可以这样写：
```javascript
    import {detectCats, Kittydar} from "kittydar.js";
```
当你运行的模块中包含一条import声明时，首先会加载被导入的模块；然后依赖图的深度优先遍历按顺序执行每一个模块的主体代码；为了避免形成回环，所有已执行的模块都会被忽略。

这些就是模块的基本知识了，相当简单吧。;-)

## Export列表
你不需要标记每一个被导出的特性，你只需要在花括号中按照列表的格式写下你想导出的所有名称：
```javascript 
export {detectCats, Kittydar};
    // 此处不需要 `export`关键字
    function detectCats(canvas, options) { ... }
    class Kittydar { ... }
```
export列表可以在模块文件最外层作用域的每一处声明，不一定非要把它放在模块文件的首行。你也可以声明多个export列表，甚至通过其它的export声明打造一个混合的export列表，只要保证每一个被导出的名称是唯一的即可。

## 重命名import和export

恰恰有时候，导出的名称会与你需要使用的其它名称产生冲突，ES6为你提供了重命名的方法解决这个问题，当你在导入名称时可以这样做：
```javascript
    // suburbia.js
    // 这两个模块都会导出以`flip`命名的东西。
    // 要同时导入两者，我们至少要将其中一个的名称改掉。
    import {flip as flipOmelet} from "eggs.js";
    import {flip as flipHouse} from "real-estate.js";
    ...
```
同样，当你在导出的时候也可以重命名。你可能会想用两个不同的名称导出相同的值，这样的情况偶尔也会遇到：
```javascript
    // unlicensed_nuclear_accelerator.js - 无DRM（数字版权管理）的媒体流
    // （这不是一个真实存在的库，但是或许它应该被做成一个库）

    function v1() { ... }
    function v2() { ... }

    export {
      v1 as streamV1,
      v2 as streamV2,
      v2 as streamLatestVersion
    };
```
## Default exports
现在广泛使用的模块系统有CommonJS、AMD两种，设计出来的新标准可以与这两种模块进行交互。所以假设你有一个Node项目，你已经执行了npm install lodash，你的ES6模块可以从Lodash中导入独立的函数：
```javascript
    import {each, map} from "lodash";

    each([3, 2, 1], x => console.log(x));
```
但是也许你已经习惯看到_.each的书写方式而不想直接用each函数呢？或者你就真的想导入整个_函数呢，毕竟_对于Lodash而言至关重要。

针对这种情况，你可以换用一种稍微不太一样的方法：不用花括号来导入模块。
```javascript
    import _ from "lodash";
```
这种简略的表达方法等价于`import {default as _} from "lodash"`;。在ES6的模块中导入的CommonJS模块和AMD模块都有一个默认的导出，如果你用require()加载这些模块也会得到相同的结果——exports对象。

ES6模块不只导出CommonJS模块，它的设计逻辑为你提供导出不同内容的多种方法，默认导出的是你得到的所有内容。举个例子，在用这种写法的时候，据我所知，著名的colors包就没有任何针对ES6的支持。像大多数npm上的包一样，它是诸多CommonJS模块的集合，但是你可以正确地将它导入到你的ES6代码中。
```javascript
    // `var colors = require("colors/safe");`的ES6等效代码
    import colors from "colors/safe";
```
如果你想让自己的ES6模块有一个默认的导出，实现的方法很简单，默认导出与其它类型的导出相似，没有什么技巧可言，唯一的不同之处是它被命名为“default”。你可以用我们刚才讨论的重命名语法来实现：
```javascript
    let myObject = {
      field1: value1,
      field2: value2
    };
    export {myObject as default};
```
这种简略的表达方法看起来更清爽：
```javascript
    export default {
      field1: value1,
      field2: value2
    };
```
关键字export default后可跟随任何值：一个函数、一个类、一个对象字面量，只要你能想到的都可以。

## 模块对象
很抱歉新特性有点儿多，但JavaScript不是唯一这样做的语言：出于某些原因，每一种语言中的模块系统都有这么一堆又独立又小，虽然无聊但是很方便的特性。不过还好，我们只剩一样东西没讲了。好吧，是两样。
```javascript
    import * as cows from "cows";
```
当你import *时，导入的其实是一个模块命名空间对象，模块将它的所有属性都导出了。所以如果“cows”模块导出一个名为moon()的函数，然后用上面这种方法“cows”将其全部导入后，你就可以这样调用函数了：cows.moo()。

*补充*: [ECMAScript6教程 模块 Modules　(二)](https://yigrherb.com/2015/12/28/ecmascript6-tutorial-module-modules-two.html)