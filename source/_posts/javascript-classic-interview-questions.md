---
title: JavaScript经典面试题
date: 2014-08-25 21:51:00
updated: 2015-09-17 21:52:58
tags: 
- css3
categories: 
- css

---
为了增加你对JavaScript这门语言的理解，本文继续使用5个面试题来考察JavaScript中的部分重要概念，主要包括JavaScript中的闭包概念、数据类型、事件循环和一个简单的算法题，希望能够让你注意到JavaScript这门语言与其他语言不同的地方。

<!--more-->


## 面试题

### 1. 闭包 Closures

阅读下面的代码：

	var nodes = document.getElementsByTagName('button');
	for (var i = 0; i < nodes.length; i++) {
	   nodes[i].addEventListener('click', function() {
	      console.log('You clicked element #' + i);
	   });
	}

当用户点击第一个和第四个按钮的时候控制台分别打印的结果是什么？为什么？

解答：

上面的代码考察了一个非常重要的概念：闭包。如果你对闭包不是非常了解，可以参考我翻译过的[这篇文章](http://wwsun.me/posts/javascript-closure.html)，也可以阅读[另一篇文章](http://www.sitepoint.com/javascript-closures-demystified/)。

上面的代码会打印两次`You clicked element #NODES_LENGTH`, 其中`NODES_LENGTH`为所获取到的button的个数。

该问题的原因在于'addEventListener'是闭包，其中的回调函数是匿名函数，而不是闭包对象。在闭包中一共创建了'NODES_LENGTH'个匿名函数，但是它们都共享同一个环境，`i`是在闭包中的变量。在`addEventListener`的回调执行时，循环已经结束，此时的变量`i`被赋值为`NODES_LENGTGH`。

### 2. 还是闭包

修复问题1的代码，使其按照我们的设想运行，即第一个按钮打印0，第二个按钮打印1等等。

解答：

这个问题可以有多种解答方法，下面主要使用两种方法解决这个问题。

解法1：使用IIFE再创建一个闭包，这样`i`的值就不会被影响。如下：

	var nodes = document.getElementsByTagName('button');
	for (var i = 0; i < nodes.length; i++) {
	   nodes[i].addEventListener('click', (function(i) {
	      return function() {
	         console.log('You clicked element #' + i);
	      }
	   })(i));
	}

解法2：将函数移动到循环外部即可（这就创建了一个新的闭包对象）

	function handlerWrapper(i) {
	   return function() {
	      console.log('You clicked element #' + i);
	   }
	}
	 
	var nodes = document.getElementsByTagName('button');
	for (var i = 0; i < nodes.length; i++) {
	   nodes[i].addEventListener('click', handlerWrapper(i));
	}

### 3. 数据类型

阅读下面的代码，给出输出结果：

	console.log(typeof null);  // object
	console.log(typeof {});	// object
	console.log(typeof []);	// object
	console.log(typeof undefined);	// undefined

解答：

这里主要测试的是对`typeof`操作符的理解。最容易被误解的应该是第三个输出值，可能大部分人的直觉是应该返回`Array`。假如你想测试一个变量是否包含数组，你可以使用下面的测试代码：

	var myArray = [];
	if (myArray instanceof Array) {
	   // do something...
	}

`typeof`测试的是数据类型（基本数据类型 + `object`）；`instanceof`测试的是属于哪个对象的实例。

### 4. 事件循环 Event Loop

下面代码的执行结果为：

	function printing() {
	   console.log(1); 
	   setTimeout(function() { console.log(2); }, 1000); 
	   setTimeout(function() { console.log(3); }, 0); 
	   console.log(4);
	}
	 
	printing();

解答：

执行结果为：

	1
	4
	3
	2

要想知道为什么输出顺序是这样的，你就得深入的了解`setTimeout()`做了什么，以及浏览器的[事件循环](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop)机制。

`setTimeout()`函数属于window对象，用来进行超时调用。它接受两个参数：要执行的代码和以毫秒表示的时间（即在执行代码前需要等待多时毫秒）。JavaScript是一个单线程序的解释器，因此同一时间内只能执行一段代码。为了控制要执行的代码，就有一个JavaScript任务队列。这些任务会按照它们添加到队列的顺序执行。

`setTimeout()`在指定时间后将任务插入任务队列。如果队列是空的，那么添加的代码会立即执行；如果队列不是空的，那么就要等前面的代码执行完了以后再执行。所以关于计时器你需要记住的是：

> 指定的时间间隔表示何时将定时器的代码添加到队列，而不是何时实际的执行代码。

因此，当调用`setTimeout()`时，即使延迟被设定为0，其回调也会被插入到队列中。回调处于队列中，直到被JS引擎分配到它，才会被执行。也就是说，即使`setTimeout`的延迟为0，其回调也会被加入到那些没有延迟的函数队列之后。

### 5. 算法
写一个函数`isPrime()`判断一个数是否为素数，如果是返回`true`，如果不是返回`false`。

解答：

回答这个题目首先要注意的是，这是在使用JavaScript进行编程，因此你不能信任传入的参数类型。因此，你永远都要检查函数的输入。

其次需要注意的是，负数一定不是素数，1和0也不是，因此首先测试这些数字。其次，偶数中只有2是素数，因此，其他的偶数你无需判断。

最后一点需要注意的是，通常你只需判断到给定数的根值即可。整理之后，很容易写出下面的程序：

	function isPrime(number) {

	   // If your browser doesn't support the method Number.isInteger of ECMAScript 6,
	   // you can implement your own pretty easily
	   if (typeof number !== 'number' || !Number.isInteger(number)) {
	      // Alternatively you can throw an error.
	      return false;
	   }
	 
	   if (number < 2) {
	      return false;
	   }
	    
	   if (number === 2) {
	      return true;
	   } else if (number % 2 === 0) {
	      return false;
	   }
	 
	   var squareRoot = Math.sqrt(number);
	   for(var i = 3; i <= squareRoot; i += 2) {
	      if (number % i === 0) {
	         return false;
	      }
	   }
	 
	   return true;
	}


### 声明

本文翻译自： http://www.sitepoint.com/5-javascript-interview-exercises/

## References

1. https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop