---
title: Javascript Tips 11-20[jstips]
date: 2016-10-18 21:33:00
updated: 2016-10-23 17:04:43
tags: 
- golang
- vscode
categories: 
- go

---
#11 提升(hoisting)

[原文位置](https://github.com/loverajoel/jstips/blob/gh-pages/_posts/en/2016-01-11-hoisting.md)

理解[提升](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var#var_hoisting)能够更好的帮助你组织函数的作用域。切记，在最开始声明变量和定义函数。变量定义和声明并不是一回事，尽管你能将它们同时执行在同一行中。声明会令系统知道变量的存在，而定义仅仅只是赋值操作而已。


<!--more-->


```js
function doTheThing() {
  // ReferenceError: notDeclared is not defined
  console.log(notDeclared);

  // Outputs: undefined
  console.log(definedLater);
  var definedLater;

  definedLater = 'I am defined!'
  // Outputs: 'I am defined!'
  console.log(definedLater)

  // Outputs: undefined
  console.log(definedSimulateneously);
  var definedSimulateneously = 'I am defined!'
  // Outputs: 'I am defined!'
  console.log(definedSimulateneously)

  // Outputs: 'I did it!'
  doSomethingElse();

  function doSomethingElse(){
    console.log('I did it!');
  }

  // TypeError: undefined is not a function
  functionVar();

  var functionVar = function(){
    console.log('I did it!');
  }
}
```

为了令代码阅读清楚些，在函数方法的最开始声明所有需要用到的变量方法，这样可以更清楚的了解各个变量的作用域。声明变量在应用之前，定义函数方法在最后，令其更清晰。

#12  ES6函数中的默认参数
[原文地址](https://github.com/loverajoel/jstips/blob/gh-pages/_posts/en/2016-01-12-pseudomandatory-parameters-in-es6-functions.md)

许多编程语言中函数方法的参数在默认情况下是强制设定的，程序员必须**显示定义**参数为可选。在JS中函数参数是可选的，但是我们可以利用**ES6的默认参数值**特性，不用在函数体内做一系列复杂操作。

```js
const _err = function( message ){
  throw new Error( message );
}

const getSum = (a = _err('a is not defined'), b = _err('b is not defined')) => a + b

getSum( 10 ) // throws Error, b is not defined
getSum( undefined, 10 ) // throws Error, a is not defined
```

`_err`函数会在`getSum`函数方法没有参数值传入时立即抛出异常。当然，你可以在[MDN](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/default_parameters)中看到关于默认参数值特性的例子。

# 13 测量JS执行效率的小技巧
[原文地址](https://github.com/loverajoel/jstips/blob/gh-pages/_posts/en/2016-01-13-tip-to-measure-performance-of-a-javascript-block.md)

为了快速衡量JavaScript的性能，我们可以使用`console`方法比如`console.time(label)`和`console.timeEnd(label)`

```js
console.time("Array initialize");
var arr = new Array(100),
    len = arr.length,
    i;

for (i = 0; i < len; i++) {
    arr[i] = new Object();
};
console.timeEnd("Array initialize"); // Outputs: Array initialize: 0.711ms
```

更多信息： [Console object](https://github.com/DeveloperToolsWG/console-object), [JavaScript benchmarking](https://mathiasbynens.be/notes/javascript-benchmarking)
Demo: [jsfiddle](https://jsfiddle.net/meottb62/)-[codepen](http://codepen.io/anon/pen/JGJPoa)(在浏览器中console)

#14 胖箭头函数
作为ES6的新属性，使用胖箭头函数可以更方便的写出更简洁的代码。这个名字来源于其语法中的与`->`（瘦）相比的**`=>`（胖）**表达式。一些程序员可能已经在**Haskell**这类编程语言中了解了这种类型的**匿名函数**或**lambda表达式**。
##箭头函数好处在哪？
* 语法： 更少的代码行，不需要一遍遍的写`function`关键字
* 语义： 自动绑定上层作用域`this`关键字。避免函数内莫名绑定到`window`。

##简单小例子
看看下面执行功能相同的代码片段，相信你会很快理解箭头函数的用法的。

```js
// general syntax for fat arrow functions
// 正常箭头函数语法
param => expression

// may also be written with parentheses
// parentheses are required on multiple params
// 多函数的箭头函数
(param1 [, param2]) => expression


// using functions
// ES5
var arr = [5,3,2,9,1];
var arrFunc = arr.map(function(x) {
  return x * x;
});
console.log(arr)

// using fat arrow
// ES6
var arr = [5,3,2,9,1];
var arrFunc = arr.map((x) => x*x);
console.log(arr)
```

## `this`绑定
我们使用箭头函数的另一理由是其可以对`this`上下文关系的处理。在箭头函数中，我们不再需要担心`.bind(this)`或设定`that=this`。箭头函数会自动绑定上级词法作用域的`this`。

```js
// globally defined this.i
this.i = 100;

var counterA = new CounterA();
var counterB = new CounterB();
var counterC = new CounterC();
var counterD = new CounterD();

// bad example
function CounterA() {
  // CounterA's `this` instance (!! gets ignored here)
  this.i = 0;
  setInterval(function () {
    // `this` refers to global object, not to CounterA's `this`
    // therefore starts counting with 100, not with 0 (local this.i)
    this.i++;
    document.getElementById("counterA").innerHTML = this.i;
  }, 500);
}

// manually binding that = this
function CounterB() {
  this.i = 0;
  var that = this;
  setInterval(function() {
    that.i++;
    document.getElementById("counterB").innerHTML = that.i;
  }, 500);
}

// using .bind(this)
function CounterC() {
  this.i = 0;
  setInterval(function() {
    this.i++;
    document.getElementById("counterC").innerHTML = this.i;
  }.bind(this), 500);
}

// fat arrow function
function CounterD() {
  this.i = 0;
  setInterval(() => {
    this.i++;
    document.getElementById("counterD").innerHTML = this.i;
  }, 500);
}
```

可以去[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)了解更多箭头函数的内容，不同的语法选择可以来[这里](http://jsrocks.org/2014/10/arrow-functions-and-their-scope/)

#15 `indexOf`用作包含条款最佳实践
JavaScript本身默认没有判断**字符串或数组**是否包含子串及元素的方法。通常我们会这么做：

```js
var someText = 'javascript rules';
if (someText.indexOf('javascript') !== -1) {
}

// or
if (someText.indexOf('javascript') >= 0) {
}
```

让我们看看[express.js](https://github.com/strongloop/express)的语法段。
[examples/mvc/lib/boot.js](https://github.com/strongloop/express/blob/2f8ac6726fa20ab5b4a05c112c886752868ac8ce/examples/mvc/lib/boot.js#L26)

```js
for (var key in obj) {
  // "reserved" exports
  if (~['name', 'prefix', 'engine', 'before'].indexOf(key)) continue;
```

[lib/utils.js](https://github.com/strongloop/express/blob/2f8ac6726fa20ab5b4a05c112c886752868ac8ce/lib/utils.js#L93)

```js
exports.normalizeType = function(type){
  return ~type.indexOf('/')
    ? acceptParams(type)
    : { value: mime.lookup(type), params: {} };
};
```

[examples/web-service/index.js](https://github.com/strongloop/express/blob/2f8ac6726fa20ab5b4a05c112c886752868ac8ce/examples/web-service/index.js#L35)

```js
// key is invalid
if (!~apiKeys.indexOf(key)) return next(error(401, 'invalid api key'));
```

相信你也注意到了，重点在于`~`操作符。按位操作符以二进制形式执行操作，但是返回标准JavaScript数据值。
它会将将**-1**转化成**0**，并且**0**在JavaScript中表示**`false`**.

```js
var someText = 'text';
!!~someText.indexOf('tex'); // someText contains "tex" - true
!~someText.indexOf('tex'); // someText NOT contains "tex" - false
~someText.indexOf('asd'); // someText doesn't contain "asd" - false
~someText.indexOf('ext'); // someText contains "ext" - true
```

##`String.prototype.includes()`
ES6引入[includes()方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/includes)你可以使用其判断字符串是否包含某个子串。

```js
'something'.includes('thing'); // true
```

在ES7中，你甚至可以将其应用到数组类型中

```js
//ES7
!!~[1, 2, 3].indexOf(1); // true
[1, 2, 3].includes(1); // true
```

很不幸，只用**Chrome, Firefox, Safari 9+及Edge**支持该语法，**IE11以下版本不支持**。推荐在可控环境下使用该方法。

#16 给回调函数传参
默认情况下你没有办法给回调函数传递参数，比如：

```js
function callback() {
  console.log('Hi human');
}

document.getElementById('someelem').addEventListener('click', callback);
```

可以利用JavaScript闭包作用域传递参数给回调函数。

```js
function callback(a, b) {
  return function() {
    console.log('sum = ', (a+b));
  }
}
var x = 1, y = 2;
document.getElementById('someelem').addEventListener('click', callback(x, y));
```

## 闭包是什么？
闭包是指可以包含独立(自由)变量的函数。换句话说，定义在闭包中的函数’记住‘创建了它的上层作用域。去[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)了解更多。
所以，通过闭包我们可以在回调函数被调用时将参数x和y传递给它。
另外也可以通过`bind`方法传递给它。

```js
var alertText = function(text) {
  alert(text);
};

document.getElementById('someelem').addEventListener('click', alertText.bind(this, 'hello'));
```

上述两种方法的效率仅仅有稍许区别，可以通过[jsperf](http://jsperf.com/bind-vs-closure-23)查看。

#17 NodeJS 执行未`require`的模块
根据你的代码是运行在`require('./something.js')`还是`node something.js`，你可以要求程序执行两种不同情况。如果你想与你的模块独立交互，这么做是非常有用的。

```js
if (!module.parent) {
    // ran with `node something.js`
    app.listen(8088, function() {
        console.log('app listening on port 8088');
    })
} else {
    // used with `require('/.something.js')`
    module.exports = app;
}
```

更多关于[模块](https://nodejs.org/api/modules.html#modules_module_parent)信息

#18 舍入的快速方法
今天的建议是关于效率的。[Ever came across the double tilde](http://stackoverflow.com/questions/5971645/what-is-the-double-tilde-operator-in-javascript)`~~` 操作符？同时也被称为双非操作符。你可以用其替换`Math.floor()`。
一位转变`~`会将32位二进制数转换为`-(input+1)`。因此双非转变会将原数据变为`-(-(input+1)+1)`，使用其进行向下取整非常方便。对输入的数值使用`~~`操作符，模仿使用`math.ceil()`对负数取整，`Math.floor()`对正数取整。当失败返回0，与之相比`Math.floor()`方法失败返回的`Nan`简直非人类。

```js
// single ~
console.log(~1337)    // -1338

// numeric input
console.log(~~47.11)  // -> 47
console.log(~~-12.88) // -> -12
console.log(~~1.9999) // -> 1
console.log(~~3)      // -> 3

// on failure
console.log(~~[]) // -> 0
console.log(~~NaN)  // -> 0
console.log(~~null) // -> 0

// greater than 32 bit integer fails
console.log(~~(2147483647 + 1) === (2147483647 + 1)) // -> 0
```

尽管，`~~`可能效率更高，但是为了可读性更好最好还是使用`Math.floor()`

#19 字符串安全连接
你可能希望将多种不同类型的变量连接成字符串。可以肯定的是，应用算术操作符不一定都适用，因此推荐使用`concat`方法。

```js
var one = 1;
var two = 2;
var three = '3';

var result = ''.concat(one, two, three); //"123"
```

应用`concat`可以准确达成你的需求，相比使用`+`操作符可能会产生预料之外的结果：

```js
var one = 1;
var two = 2;
var three = '3';

var result = one + two + three; //"33" instead of "123"
```

关于效率问题，`join`方法和`concat`效率基本一致。

#20 通过返回对象以实现函数链
在面向对象的JavaScript中创建函数，函数内返回对象使你可以类似**JQuery**的函数链。

```js
function Person(name) {
  this.name = name;

  this.sayName = function() {
    console.log("Hello my name is: ", this.name);
    return this;
  };

  this.changeName = function(name) {
    this.name = name;
    return this;
  };
}

var person = new Person("John");
person.sayName().changeName("Timmy").sayName();
```