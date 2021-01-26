---
title: "Javascript Tips 21-30[jstips] "
categories: [ "JS" ]
tags: [ "javascript","jstips" ]
draft: false
slug: "tips-2130jstips-jaxvascxript"
date: "2016-11-01 21:28:00"
---

#21 数组重组
这个代码段使用了[Fisher-Yates Shuffling](https://www.wikiwand.com/en/Fisher%E2%80%93Yates_shuffle)算法重组已有数组。

```js
function shuffle(arr) {
    var i,
        j,
        temp;
    for (i = arr.length - 1; i > 0; i--) {
        j = Math.floor(Math.random() * (i + 1));
        temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
    return arr;    
};
//例子
var a = [1, 2, 3, 4, 5, 6, 7, 8];
var b = shuffle(a);
console.log(b);
// [2, 7, 8, 6, 5, 3, 1, 4]
```


<!--more-->


#22 清空数组
通常你定义数组并且希望内容清空，你可能会这么做：

```js
// define Array
var list = [1, 2, 3, 4];
function empty() {
    //empty your array
    list = [];
}
empty();
```

但是，这里有更高效的方法：

```js
var list = [1, 2, 3, 4];
function empty() {
    //empty your array
    list.length = 0;
}
empty();
```

* `list = []`通过创建新数组进行赋值，然而这么做却不会对原数组内容产生影响。原数组内容仍存储在内存当中，可能会导致内存泄露。
* `list.length = 0`删除数组内内容，这样会对其引用同时产生影响。

换句话说，如果对同一数组进行了两次引用(`a=[1, 2, 3], a2 = a`)，并且你通过`list.length = 0`进行内容删除，两个引用(a 和 a2)都会为空。(因此，如果不希望产生影响，就别用上述技巧)。

想想，下例会输出什么：

```js
var foo = [1,2,3];
var bar = [1,2,3];
var foo2 = foo;
var bar2 = bar;
foo = [];
bar.length = 0;
console.log(foo, bar, foo2, bar2);

// [] [] [1, 2, 3] []
```

更多细节[difference-between-array-length-0-and-array](http://stackoverflow.com/questions/4804235/difference-between-array-length-0-and-array)

#23 字符串 => 数值
将**字符串类型**的数字变为**数值**很常见，最简单且迅速的方法建议使用**`+`**操作符。

```js
var one = '1';
var numberOne = +one // Number 1
```

当然，**`-`** 操作符也行，但是这么做会令数值为**负**

```js
var one = '1';
var negativeNumberOne = -one; // Number -1
```

#24 使用`===`代替`==`
**`==`(或`!=`)操作符**会在需要的情况下**自动进行类型转换**。
**`===`(或`!==`)操作符**不会进行类型转换。它只会比较值和类型，因此相对`==`操作符会执行更快。

```js
[10] ==  10      // is true
[10] === 10      // is false

'10' ==  10      // is true
'10' === 10      // is false

 []  ==  0       // is true
 []  === 0       // is false

 ''  ==  false   // is true but true == "a" is false
 ''  === false   // is false 
```

#25 使用立即执行函数表达式
立即执行函数表达式是缩写为IIFE(immediately invoked function expression)的匿名函数，它在JavaScript中有一些重要的作用。

```js
(function() {
 // Do something
 }
)()
```

包裹在圆括号中的匿名函数，将匿名函数编程一个函数表达式或变量表达式，而不是一个在全局（或任意）作用域的简单匿名函数。因此我们现在有一个未命名的函数表达式。
当然，我们也可以为其命名。

```js
(someNamedFunction = function(msg) {
    console.log(msg || "Nothing for today !!")
    }) (); // Output --> Nothing for today !!

someNamedFunction("Javascript rocks !!"); // Output --> Javascript rocks !!
someNamedFunction(); // Output --> Nothing for today !!
```

预知更多详细情节：URL‘s 1. [Link 1](https://blog.mariusschulz.com/2016/01/13/disassembling-javascripts-iife-syntax) 2. [Link 2](http://javascriptissexy.com/12-simple-yet-powerful-javascript-tips/)

#  26 过滤、排序字符串表
你可能有一个一大长串名单需要过滤重复元素，同时需要按照字母表排列。

在下面例子中给出了一个JavaScript**保留字**列表，但是你会发现其中有许多重复单词，并且未按序排列。因此，我们使用下列数组测试我们的小技巧。

```js
var keywords = ['do', 'if', 'in', 'for', 'new', 'try', 'var', 'case', 'else', 'enum', 'null', 'this', 'true', 'void', 'with', 'break', 'catch', 'class', 'const', 'false', 'super', 'throw', 'while', 'delete', 'export', 'import', 'return', 'switch', 'typeof', 'default', 'extends', 'finally', 'continue', 'debugger', 'function', 'do', 'if', 'in', 'for', 'int', 'new', 'try', 'var', 'byte', 'case', 'char', 'else', 'enum', 'goto', 'long', 'null', 'this', 'true', 'void', 'with', 'break', 'catch', 'class', 'const', 'false', 'final', 'float', 'short', 'super', 'throw', 'while', 'delete', 'double', 'export', 'import', 'native', 'public', 'return', 'static', 'switch', 'throws', 'typeof', 'boolean', 'default', 'extends', 'finally', 'package', 'private', 'abstract', 'continue', 'debugger', 'function', 'volatile', 'interface', 'protected', 'transient', 'implements', 'instanceof', 'synchronized', 'do', 'if', 'in', 'for', 'let', 'new', 'try', 'var', 'case', 'else', 'enum', 'eval', 'null', 'this', 'true', 'void', 'with', 'break', 'catch', 'class', 'const', 'false', 'super', 'throw', 'while', 'yield', 'delete', 'export', 'import', 'public', 'return', 'static', 'switch', 'typeof', 'default', 'extends', 'finally', 'package', 'private', 'continue', 'debugger', 'function', 'arguments', 'interface', 'protected', 'implements', 'instanceof', 'do', 'if', 'in', 'for', 'let', 'new', 'try', 'var', 'case', 'else', 'enum', 'eval', 'null', 'this', 'true', 'void', 'with', 'await', 'break', 'catch', 'class', 'const', 'false', 'super', 'throw', 'while', 'yield', 'delete', 'export', 'import', 'public', 'return', 'static', 'switch', 'typeof', 'default', 'extends', 'finally', 'package', 'private', 'continue', 'debugger', 'function', 'arguments', 'interface', 'protected', 'implements', 'instanceof'];
```

因为我们不希望更改原生列表，所以我们准备使用`filter`方法，它将会基于我们的传入的**过滤方法**返回过滤后数组。这个比较方法会比较原生数组当前位置的保留字与新生成数组索引，并且会在符合要求情况下添加进新数组中。

最后，我们会排序过滤后数组使用`sort`方法，我们会为其传入比较方法，要求其返回按字母序排列的数组。

```js
var filteredAndSortedKeywords = keywords
  .filter(function (keyword, index) {
      return keywords.lastIndexOf(keyword) === index;
    })
  .sort(function (a, b) {
      return a < b ? -1 : 1;
    });
```

ES6版本，我们使用**箭头函数**来传递比较方法：

```js
const filteredAndSortedKeywords = keywords
  .filter((keyword, index) => keywords.lastIndexOf(keyword) === index)
  .sort((a, b) => a < b ? -1 : 1);
```

最后，我们可以得到去重且重排序的JavaScript保留字数组。

# 27 短路求值
[短路求值](https://zh.wikipedia.org/wiki/%E7%9F%AD%E8%B7%AF%E6%B1%82%E5%80%BC)的第二个参数被执行或者返回值，必须要看第一个参数值是否满足表达式要求。

* 在AND(&&)方法中当第一个参数为假时，则整个返回值必为**假**。
* 在OR(||)方法中，当第一个参数为真时，则返回值必为**真**。

```js
var test = true;
var isTrue = function(){
  console.log('Test is true.');
};
var isFalse = function(){
  console.log('Test is false.');
};

//Using logical AND - &&.
// A normal if statement.
if(test){
  isTrue();    // Test is true
}

// Above can be done using '&&' as -

( test && isTrue() );  // Test is true


//Using logical OR - ||.

test = false;
if(!test){
  isFalse();    // Test is false.
}

( test || isFalse());  // Test is false.
```

逻辑或方法，也可以被用来为函数设默认值

```js
function theSameOldFoo(name){ 
    name = name || 'Bar' ;
    console.log("My best friend's name is " + name);
}
theSameOldFoo();  // My best friend's name is Bar
theSameOldFoo('Bhaskar');  // My best friend's name is Bhaskar
```

逻辑与方法，可以被用来避免属性值未定义的情况：

```js
var dog = { 
  bark: function(){
     console.log('Woof Woof');
   }
};

// Calling dog.bark();
dog.bark(); // Woof Woof.

//But if dog is not defined, dog.bark() will raise an error "Cannot read property 'bark' of undefined."
// To prevent this, we can you &&.

dog&&dog.bark();   // This will only call dog.bark(), if dog is defined.
```

# 28 科里化 VS 部分应用程序
##科里化
科里化会将一个函数传入另一个函数中。

```js
f  :  X * Y -> R
f' :  X -> ( Y -> R)
```

与第一个函数`f`传入两个参数不同，我们给`f'`传递一个参数。其返回结果是一个函数方法，我们会将第二个参数传递给它，然后求得结果。
所以，我们调用未科里化的`f`方法，以下例形式：

```js
f(2, 3);
```

应用科里化的`f'`函数以下述形式：

```js
f’(2)(3);
```

下面给出具体例子：

```js
//未科里化
function add(x, y) {
  return x + y;
}

add(3, 5);   // returns 8

//科里化
function addC(x) {
  return function (y) {
    return x + y;
  }
}

addC(3)(5);   // returns 8
```

###科里化算法
科里化是一个二元函数，其返回一个函数，然后该函数再返回一个函数。
`curry : (X * Y -> R) -> (X -> (Y -> R)) `

```js
function curry(f) {
  return function(x) {
    return function(y) {
      return f(x, y);
    }
  }
}
```

##局部应用
局部应用传入一个函数
`f: X * Y -> R`
并且设定固定值与第一个参数计算，产生新函数。
`f' Y -> R`
**f'**方法与**f**方法作用相同，只是将第二个参数放入返回的第二个方法中传入。
例如，下面例子将第一个参数传入新方法，并设定固定值5与其相加：

```js
function plus5(y) {
  return 5 + y;
}

plus5(3);  // returns 8
```

###部分应用算法
partApply方法传入一个二元函数方法和一个参数，返回一个一元函数。
`((X * Y -> R)) -> (X * (Y -> R))`

```js
function partApply(f, x) {
  return function(y) {
    return f(x, y);
  }
}
```

#29 通过记忆数据加快递归函数 
大家对斐波那契数列都很熟悉了，我们看以在20秒就写出下列函数

```js
var fibonacci = function(n){
    return n < 2 ? n : fibonacci(n-1) + fibonacci(n-2);
}
```

它很有用，但是因为做了过多重复计算工作所以没效率。我们可以通过存下其之前的计算结果来增速计算：

```js
var fibonacci = (function(){
    var cache = {
        0: 0,
        1: 1
    };
    return function self(n){
        return n in cache ? cache[n] : (cache[n] = self(n-1) + self(n-2));
    }
})()
```

我们也可以定义一个更高级接收函数作为参数的方法，并且返回之前存储下的该方法。

```js
//ES5

var memoize = function(func){
    var cache = {};
    return function(){
        var key = Array.prototype.slice.call(arguments).toString();
        return key in cache ? cache[key] : (cache[key] = func.apply(this,arguments));
    }
}
fibonacci = memoize(fibonacci);

//ES6

var memoize = function(func){
    const cache = {};
    return (...args) => {
        const key = [...args].toString();
        return key in cache ? cache[key] : (cache[key] = func(...args));
    }
}
fibonacci = memoize(fibonacci);
```

我们可以在多种情况下使用`memoize()`方法。
* GCD（最大公约数）
* 阶乘计算

```js
//Greatest Common Divisor
var gcd = memoize(function(a,b){
    var t;
    if (a < b) t=b, b=a, a=t;
    while(b != 0) t=b, b = a%b, a=t;
    return a;
})
gcd(27,183); //=> 3

//Factorial calculation
var factorial = memoize(function(n) {
    return (n <= 1) ? 1 : n * factorial(n-1);
})
factorial(5); //=> 120
```

# 30 转换真假值为布尔型
你可以转换真、假值为**真**布尔型，通过`!!`操作符。

```js
!!"" // false
!!0 // false
!!null // false
!!undefined // false
!!NaN // false

!!"hello" // true
!!1 // true
!!{} // true
!![] // true
```

原文：[jstips](http://www.jstips.co/)