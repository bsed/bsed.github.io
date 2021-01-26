---
title: "Javascript Tips 31-38[jstips] "
categories: [ "JS" ]
tags: [ "javascript","jstips" ]
draft: false
slug: "tips-3138jstips-jaxvascxript"
date: "2016-11-02 21:33:00"
---

## 31 避免更改或传递参数到`arguments`-非最优化
##背景
在JavaScript中，`arguments`变量可以获取传递给函数方法所有的参数。`arguments`是一个类数组对象，可以使用数组的操作符进行操作，同时它也有`length`属性。但是它没有数组内置方法，例如`filter`、`map`和`forEach`。因此，常见方法是将`arguments`转变为数组类型：}

```js
var args = Array.prototype.slice.call(arguments);
```

通过调用数组的`slice`方法传递`arguments`的值。但是，`slice`方法只是返回`arguments`的浅拷贝到新建数组。简写方法如下：

```js
var args = [].slice.call(arguments);
```


<!--more-->


通过简单的数组符号调用`slice`方法即可。
## 优化
在函数内使用`arguments`都会导致Chrome的**V8**引擎和**Node**跳过函数优化部分，因此导致执行效率较差。可以阅读[优化杀手](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)。传递`arguments`给任意函数都会导致`arguments`泄露。
当然，如果想要`arguments`的数组，允许你是用这个方法：

```js
var args = new Array(arguments.length);
for(var i = 0; i < args.length; ++i) {
  args[i] = arguments[i];
}
```

恩，这样虽然很啰嗦，但是生产环境代码效率高于一切。

## 32 使用Map解决对象属性无序问题
## 对象属性顺序
> 一个对象是类型对象的一个成员。它是无序属性集合，每个属性都包含一个初始值、对象或方法。存储在对象内的函数被称为方法。

看下面例子，每个浏览器都有其自己的排序规则，因此对象的顺序没法固定。

```js
var myObject = {
    z: 1,
    '@': 2,
    b: 3,
    1: 4,
    5: 5
};
console.log(myObject) // Object {1: 4, 5: 5, z: 1, @: 2, b: 3}

for (item in myObject) {...
// 1
// 5
// z
// @
// b
```

## 如何解决？

### Map

我们可以通过使用ES6的新特性—`Map`。Map对象根据其添加顺序进行排序。使用`for ... of`循环方法返回包含每个元素的**键值对**数组

```js
var myObject = new Map();
myObject.set('z', 1);
myObject.set('@', 2);
myObject.set('b', 3);
for (var [key, value] of myObject) {
  console.log(key, value);
...
// z 1
// @ 2
// b 3
```

###老版本浏览器解决办法
**Mozilla 建议**
> 如果想要跨平台并且有序的将执行对象内的值或方法，可以通过使用两数组，分别按顺序存储关键字及相对应的值的方式来达成。

```js
// Using two separate arrays
var objectKeys = [z, @, b, 1, 5];
for (item in objectKeys) {
    myObject[item]
...

// Build an array of single-property objects
var myData = [{z: 1}, {'@': 2}, {b: 3}, {1: 4}, {5: 5}];
```

#33 产生从0到N-1位数的最简函数
通过下面一行代码，我们可以创建从0到N-1范围的数字组成的数组

```js
Array.apply(null, {length: N}).map(Number.call, Number);
```

让我们详细分析该代码，在JavaScript中，我们通过`call()`方法调用函数，传入的第一个参数是用于说明函数作用域，后续参数则为传入该函数作用域的参数值。

```js
function add(a, b){
    return (a+b);
}
add.call(null, 5, 6);
```

上例函数会返回5，6的和。
JavaScript的数组的`map()`方法，要求传入两个参数。第一个参数为回调函数，第二个参数则为作用域。**回调函数**要求传入三个参数,`值`、`索引值`和我们想要操作的`数组`。所以，下面为示例语法：

```js
[1, 2, 3].map(function(value, index, arr){
    //Code
}, this);
```

下面代码我们创建大小为N的数组：

```js
Array.apply(null, {length: N})
```

下面的方法将所有元素传入创建的新数组：

```js
Array.apply(null, {length: N}).map(Number.call, Number);
```

如果，你希望为从1开始到N的所组成的数组：

```js
Array.apply(null, {length: N}).map(function(value, index){
  return index+1;  
});
```



## 34 实现异步循环

让我们尝试实现一个每秒输出一个递增值的异步函数：

```js
for (var i=0; i<5; i++) {
    setTimeout(function(){
        console.log(i); 
    }, 1000);
}  
```

但是，结果很蛋疼，没按照要求工作

```js
> 5
> 5
> 5
> 5
> 5
```

**原因：**
每秒调用的不是i的拷贝，是i值的引用。因此 **`i`** 循环递增,函数执行获取到i值为5。
这个问题看起来解决很容易，通过将i每次期望执行到的值存储到一个临时变量中。

```js
for (var i=0; i<5; i++) {
    var temp = i;
    setTimeout(function(){
        console.log(temp); 
    }, 1000);
}  
```

**但是，结果依然不对**

```js
> 4
> 4
> 4
> 4
> 4
```

**解决办法**
换个方法复制i值。最常见方法是通过声明函数创建闭包并将i值作为参数传递，下面给出自调用方法

```js
for (var i=0; i<5; i++) {
    (function(num){
        setTimeout(function(){
            console.log(num); 
        }, 1000); 
    })(i);  
}  
```

在JavaScript中，函数参数仅仅是传值。因此，原生类型都是传递值拷贝。在函数内对参数进行更改不会影响到外部作用域。**对象类型特殊些，对其内容更改会在所有作用域产生影响。**

另一个解决办法是通过使用`let`，使用ES6的`let`关键字，因为它不同于`var`，它会限定作用域。

```js
for (let i=0; i<5; i++) {
    var temp = i;
    setTimeout(function(){
        console.log(temp); 
    }, 1000);
}  
```

## 35 赋值操作符
赋值操作非常常见，有时对于我们这些懒惰的程序员来说打字变得耗时了。因此我们会使用一些小技巧来使我们的代码简洁。

```js
x += 23; // x = x + 23;
y -= 15; // y = y - 15;
z *= 10; // z = z * 10;
k /= 7; // k = k / 7;
p %= 3; // p = p % 3;
d **= 2; // d = d ** 2;
m >>= 2; // m = m >> 2;
n <<= 2; // n = n << 2;
n ++; // n = n + 1;
n --; n = n - 1;
```

## `If-else`使用三元操作符
常见写法：

```js
var newValue;
if(value > 10) 
  newValue = 5;
else
  newValue = 2;
```

使用三元操作符：

```js
var newValue = (value > 10) ? 5 : 2;
```

## Null, Undefined, Empty 检测

```js
//常见写法
if (variable1 !== null || variable1 !== undefined || variable1 !== '') {
     var variable2 = variable1;
}

//缩写
var variable2 = variable1  || '';
```

## 对象数组表示法

```js
//Common 
var a = new Array();
a[0] = "myString1";
a[1] = "myString2";

//缩写
var a = ["myString1", "myString2"];
```

## 关联数组

```js
// Common 
var skillSet = new Array();
skillSet['Document language'] = 'HTML5';
skillSet['Styling language'] = 'CSS3';

//简写
var skillSet = {
    'Document language' : 'HTML5', 
    'Styling language' : 'CSS3'
};
```

## 36 观测DOM的扩展变化
**翻译的很差，后面会重新对其进行翻译**
[MutationObserver](https://developer.mozilla.org/en/docs/Web/API/MutationObserver)可以监听DOM变化，并且可以在他们状态变化时对其进行操作。在下例中有当第一个目标元素创建完成，准备进行创建后面一个元素时。定时器动态加载内容。扩展代码中，首先，根观察者工作直到目标元素产生，然后元素观察者开始工作。这样级联的观察帮助最后发现子目标元素的发现。这样的方式，对于扩展开发复杂网站动态加载内容很有用。

```js
const observeConfig = {
    attributes: true,
    childList: true,
    characterData: true,
    subtree: true
};

function initExtension(rootElement, targetSelector, subTargetSelector) {
    var rootObserver = new MutationObserver(function(mutations) {
        console.log("Inside root observer");
        targetElement = rootElement.querySelector(targetSelector);
        if (targetElement) {
            rootObserver.disconnect();
            var elementObserver = new MutationObserver(function(mutations) {
                console.log("Inside element observer")
                subTargetElement = targetElement.querySelector(subTargetSelector);
                if (subTargetElement) {
                    elementObserver.disconnect();
                    console.log("subTargetElement found!")
                }
            })
            elementObserver.observe(targetElement, observeConfig);
        }
    })
    rootObserver.observe(rootElement, observeConfig);
}

(function() {

    initExtension(document.body, "div.target", "div.subtarget")

    setTimeout(function() {
        del = document.createElement("div");
        del.innerHTML = "<div class='target'>target</div>"
        document.body.appendChild(del)
    }, 3000);


    setTimeout(function() {
        var el = document.body.querySelector('div.target')
        if (el) {
            del = document.createElement("div");
            del.innerHTML = "<div class='subtarget'>subtarget</div>"
            el.appendChild(del)
        }
    }, 5000);

})()
```

## 37数组的删除处理
##原生类型
如果数组内仅仅包括原生数据类型，我们可以通过`filter`和`indexOf`方法对其进行删除处理。

```js
var deduped = [ 1, 1, 'a', 'a' ].filter(function (el, i, arr) {
    return arr.indexOf(el) === i;
});

console.log(deduped); // [ 1, 'a' ]
```

## ES2015
可以通过使用箭头函数将该函数写的更加紧凑小巧。

```js
var deduped = [ 1, 1, 'a', 'a' ].filter( (el, i, arr) => arr.indexOf(el) === i);

console.log(deduped); // [ 1, 'a' ]
```

但是，假如引进使用`Sets`和`from`方法，我们实现该方法更加方便

```js
var deduped = Array.from( new Set([ 1, 1, 'a', 'a' ]) );

console.log(deduped); // [ 1, 'a' ]
```

## 对象
当元素是对象时，我们没法使用上述方法，这是因为对象存储是基于引用而其他元素则是基于值拷贝存储。

```js
1 === 1 // true

'a' === 'a' // true

{ a: 1 } === { a: 1 } // false
```

因此，我们通过使用哈希表来完成同样目的

```js
function dedup(arr) {
    var hashTable = {};

    return arr.filter(function (el) {
        var key = JSON.stringify(el);
        var match = Boolean(hashTable[key]);

        return (match ? false : hashTable[key] = true);
    });
}

var deduped = dedup([
    { a: 1 },
    { a: 1 },
    [ 1, 2 ],
    [ 1, 2 ]
]);

console.log(deduped); // [ {a: 1}, [1, 2] ]
```

在JavaScript中，哈希表是简单的对象。`key`关键字一直是`string`类型。这意味着我们无法分辨数值和字符串的相同值，比如1和‘1’.

```js
var hashTable = {};

hashTable[1] = true;
hashTable['1'] = true;

console.log(hashTable); // { '1': true }
```

但是，由于我们使用了`JSON.stringify`。关键字为`string`类型，因此存储形式为转移字符串值，在哈希表中转化为独一无二的键值。

```js
var hashTable = {};

hashTable[JSON.stringify(1)] = true;
hashTable[JSON.stringify('1')] = true;

console.log(hashTable); // { '1': true, '\'1\'': true }
```

这意味着重复的元素相同的值，但是类型不同。仍将被相同的方法删除处理。

```js
var deduped = dedup([
    { a: 1 },
    { a: 1 },
    [ 1, 2 ],
    [ 1, 2 ],
    1,
    1,
    '1',
    '1'
]);

console.log(deduped); // [ {a: 1}, [1, 2], 1, '1' ]
```

#38 多维数组 -> 单数组
有三种常用的办法将多维数组合并为单数组。
**样例数组：**

```js
var myArray = [[1, 2],[3, 4, 5], [6, 7, 8, 9]];

//转变为

[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

##方法1 ： 使用`concat()` 和`apply()`

```js
var myNewArray = [].concat.apply([], myArray);
// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

##方法2： 使用`reduce()`方法

```js
var myNewArray = myArray.reduce(function(prev, curr) {
  return prev.concat(curr);
});
// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

##方法3： 

```js
var myNewArray3 = [];
for (var i = 0; i < myArray.length; ++i) {
  for (var j = 0; j < myArray[i].length; ++j)
    myNewArray3.push(myArray[i][j]);
}
console.log(myNewArray3);
// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

[这里](https://jsbin.com/qeqicu/edit?js,console)有上述三个方法的应用。
对无限嵌套的数组，可以尝试使用**Underscore**的[flatten](https://github.com/jashkenas/underscore/blob/master/underscore.js#L501)
[这里](http://jsperf.com/flatten-an-array-loop-vs-reduce/6)有对于三个方法表现的测试。

## 39 JavaScript高级属性
在JavaScript中，我们可以对对象`Object`中的属性进行设定。比如我们设定其为**私有**或**只读**。这一特性已经被ES5和新版本浏览器所支持。
因此，你可以通过`Object`的`defineProperty`方法使用。

```js
var a = {};
Object.defineProperty(a, 'readonly', {
  value: 15,
  writable: false
});

a.readonly = 20;
console.log(a.readonly); // 15
```

具体语法：

```js
//Single Line
Object.defineProperty(dest, propName, options)

//Multiple Line
Object.defineProperties(dest, {
  propA: optionsA,
  propB: optionsB, //...
})
```

其中`options`包括以下属性
* `value`值： 如果该属性没有`getter`方法，则`value`是强制设定属性。`{a: 12} === Object.defineProperty(obj, 'a', {value: 12})`
* `writable`属性： 设定属性为**只读**。注意，如果属性是一个嵌套对象，则其属性仍可写。
* `enumerable`：设定属性是否隐藏。这一设定用于说明`for ... of`循环和`stringify`方法将不会能遍历到这一属性，但是该属性仍存在且**可读可写**。
* `configurable`：设定属性不可修改。防止其被删除或重定义。当然，如果该属性为对象，则其内属性仍可以被更改。

因此，假如想要创建私有常量对象，你可以按照下例方式设定：

```js
Object.defineProperty(obj,
  'myPrivateProp', 
  { 
    value: val, 
    enumerable: false, 
    writable: false, 
    configurable: false
  });
```

除了设定属性，`defineProperty`允许我们设定**动态属性**，由于第二个参数是字符串。例如，我们希望根据一些额外设定创建属性。

```js
var obj = {
  getTypeFromExternal(): true // illegal in ES5.1
}

Object.defineProperty(obj, getTypeFromExternal(), {value: true}); // ok

// For the example sake, ES6 introduced a new syntax:
var obj = {
  [getTypeFromExternal()]: true
}
```

高级属性允许我们像面向对象语言那样创建`getter`和`setter`属性。因此，我们不能使用`writable`、`enumerable`和`configurable`属性，而改用：

```js
function Foobar () {
  var _foo; //  true private property

  Object.defineProperty(obj, 'foo', {
    get: function () { return _foo; }
    set: function (value) { _foo = value }
  });

}

var foobar = new Foobar();
foobar.foo; // 15
foobar.foo = 20; // _foo = 20
```

除了先进的访问器和明显封装的优势外，你会发现，我们直接通过**`.`**获得到了属性，没有调用`getter`方法。这么做非常简洁，例如我们有一个多层嵌套的对象。

```js
var obj = {a: {b: {c: [{d: 10}, {d: 20}] } } };
```

相比于`a.b.c[0].d`的语法，我们可以通过创建一个别名调用：

```js
Object.defineProperty(obj, 'firstD', {
  get: function () { return a && a.b && a.b.c && a.b.c[0] && a.b.c[0].d }
})

console.log(obj.firstD) // 10
```

##注意
如果设定了`getter`方法没有设定`setter`方法却想设定值，系统会返回错误。当使用辅助函数例如`$.extend`或`_.merge`时切记这点，注意使用。