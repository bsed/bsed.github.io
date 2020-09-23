---
title: 理解词法作用域与动态作用域 [JS基础增强]
date: 2019-03-31 10:15:00
updated: 2019-11-04 09:59:53
tags: 
- vue
- number
categories: 
- js

---
作用域有两种比较常见的模型，一种是`词法作用域`，一种是`动态作用域`，在JavaScript中采用的是词法作用域。接下来就谈谈JavaScript中的词法作用域与动态作用域。
![js_cifascope.png][1]
## 词法作用域与动态作用域

词法作用域也叫作静态作用域，在函数定义的时候函数作用域就确定好了，而动态作用域则在函数被调用的时候函数作用域才确定的，动态作用域不会关心函数和作用域是怎么声明或者是在哪里声明，它只关心函数在哪里被调用。虽然JavaScript采用的是词法作用域，但是在JavaScript中也有类似动态作用域的实现机制，即JavaScript中的`this`。


<!--more-->


举个简单的例子来理解一下词法作用域与动态作用域的区别：

```javascript
function foo() {
    console.log(a)
}

function bar() {
    var a = 2;
    foo();
}

var a = 1;
bar();

```
我们来简单分析一下上面的代码执行结果，假如JavaScript采用的是词法作用域的话，当执行bar函数的时候，先是声明了一个局部变量a，并将它赋值为2，然后执行foo函数 ，首先在foo函数内部查找有没有变量a，如果没有，则往上一层代码中查找，在foo函数外部声明了一个变量全局变量a，并且它别赋值为1，所以执行foo函数的结果是输`1`。假如JavaScript采用的是动态作用域的话，当执行到bar函数内部的foo函数时，也会先从foo函数内部查找有没有变量a，如果没有，则继续在调用foo函数的作用域里查找，所以最终会输出`2`。

## 动态作用域

什么语言是动态作用域？

bash 就是动态作用域，不信的话，把下面的脚本存成例如 `scope.bash`，然后进入相应的目录，用命令行执行 `bash ./scope.bash`, 看看打印的值是多少。
```bash
value =1 ;
function foo(){
  echo $value
}
function bar(){
  local value = 2;
  foo
}
bar
```
## 思考题
最后，让我们看一个《javascript权威指南》中的例子：

**例子一：**
```javascript
var scope = "global scope";
function checkscope(){
  var scope = "local scope";
  function f(){
    return scope;
  }
  return f()
}
checkscope()
```

**例子二：**
```javascript
var scope = "global scope";
function checkscope(){
  var scope = "local scope";
  function f(){
    return scope;
  }
  return f;
}
checkscope()();
```
猜猜两段代码各自执行结果是多少？

这里直接告诉大家结果，两段代码都会打印： `local scope`;

原因也很简单，因为javascript采用的是词法作用域，函数的作用域基于函数创建的位置。

而引用《javascript权威指南》的回答是：

javascript 函数的执行用到了`作用域链`，这个作用域链是**在函数定义的时候创建的**。嵌套的函数`f()`定义在这个作用域链里，其中的变量 scope 一定是**局部变量**，不管何时何地执行函数 f()，这种绑定在执行f()时依然有效。

前面我们已经提到了，在JavaScript中采用的是词法作用域，所以上述例子的正确结果是输出1。通过这个简单的例子，我们也大体弄懂了词法作用域与动态作用域的区别，另外从这个作用域的回顾可以引申到变量提升、执行上下文栈、闭包等相关知识点，这些知识点都将在后续的博客中进行回顾和总结。

参考文章：https://github.com/mqyqingfeng/Blog/issues/2


  [1]: https://imgs.gnux.cn/usr/uploads/2019/03/79536372.png