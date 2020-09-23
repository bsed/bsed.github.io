---
title: ECMAScript6教程 生成器 Generators
date: 2015-11-22 16:08:00
updated: 2016-01-24 08:04:20
tags: 
- hadoop
- ntp
categories: 
- java

---
![yigrherb_generator.png][1]
> 原文: [https://hacks.mozilla.org/2015/05/es6-in-depth-generators/](https://hacks.mozilla.org/2015/05/es6-in-depth-generators/)
> 作者：[Jason Orendorff](https://blog.mozilla.org/jorendorff/)
> 翻译: 刘振涛
## 生成器（Generators）简介

什么是生成器？

我们从一个示例开始：

```javascript

    function* quips(name) {
	yield "hello " + name + "!";
	yield "i hope you are enjoying the blog posts";
	if (name.startsWith("X")) {
		yield "it's cool how your name starts with X, " + name;
	}
    yield "see you later!";
```
这段代码看起来很像一个函数，我们称之为**生成器函数**，它与普通函数有很多共同点，但是二者有如下区别：


<!--more-->


- 普通函数使用function声明，而生成器函数使用function*声明。

- 在生成器函数内部，有一种类似return的语法：关键字yield。二者的区别是，普通函数只可以return一次，而生成器函数可以yield多次（当然也可以只yield一次）。在生成器的执行过程中，遇到*yield表达式立即暂停，后续可恢复执行状态*。

## 生成器做了什么？
当你调用quips()生成器函数时发生了什么？

```javascript

	> var iter = quips("jorendorff");
	  [object Generator]
	> iter.next()
	  { value: "hello jorendorff!", done: false }
	> iter.next()
	  { value: "i hope you are enjoying the blog posts", done: false }
	> iter.next()
	  { value: "see you later!", done: false }
	> iter.next()
	  { value: undefined, done: true }

```
你大概已经习惯了普通函数的使用方式，当你调用它们时，它们立即开始运行，直到遇到return或抛出异常时才退出执行，作为JS程序员你一定深谙此道。

生成器调用看起来非常类似：quips("jorendorff")。但是，当你调用一个生成器时，它并非立即执行，而是返回一个已**暂停的生成器对象**（上述实例代码中的iter）。你可将这个生成器对象视为一次函数调用，只不过立即冻结了，它恰好在生成器函数的最顶端的第一行代码之前冻结了。

每当你调用生成器对象的.next()方法时，函数调用将其自身解冻并一直运行到下一个yield表达式，再次暂停。

这也是在上述代码中我们每次都调用iter.next()的原因，我们获得了quips()函数体中yield表达式生成的不同的字符串值。

调用最后一个iter.next()时，我们最终抵达生成器函数的末尾，所以返回结果中`done`的值为`true`。抵达函数的末尾意味着没有返回值，所以返回结果中`value`的值为`undefined`。

现在回到 [会说话的猫的demo页面](http://people.mozilla.org/~jorendorff/demos/meow.html)，尝试在循环中加入一个yield，会发生什么？

如果用专业术语描述，每当生成器执行yields语句，生成器的堆栈结构（本地变量、参数、临时值、生成器内部当前的执行位置）被移出堆栈。然而，生成器对象保留了对这个堆栈结构的引用（备份），所以稍后调用.next()可以重新激活堆栈结构并且继续执行。

值得特别一提的是，生成器不是线程，在支持线程的语言中，多段代码可以同时运行，通通常导致竞态条件和非确定性，不过同时也带来不错的性能。生成器则完全不同。当生成器运行时，它和调用者处于同一线程中，拥有确定的连续执行顺序，**永不并发**。与系统线程不同的是，生成器只有在其函数体内标记为`yield`的点才会暂停。

现在，我们了解了生成器的原理，领略过生成器的`运行`、`暂停``恢复运行`的不同状态。那么，这些奇怪的功能究竟有何用处？

## 生成器是迭代器！

我们知道通过实现`[Symbol.iterator]()`和`.next()`两个方法你就可以创建自定义迭代器。

实现一个接口不是一桩小事，我们一起实现一个迭代器。举个例子，我们创建一个简单的`range`迭代器，它可以简单地将两个数字之间的所有数相加。首先是传统C的for(;;)循环：

```javascript

	// 应该弹出三次 "ding"
	for (var value of range(0, 3)) {
	  alert("Ding! at floor #" + value);
	}

```

使用ES6的类的解决方案（如果不清楚语法细节，无须担心，我们将在接下来的文章中为你讲解）：

```javascript

	class RangeIterator {
	  constructor(start, stop) {
	    this.value = start;
	    this.stop = stop;
	  }

	  [Symbol.iterator]() { return this; }

	  next() {
	    var value = this.value;
	    if (value < this.stop) {
	      this.value++;
	      return {done: false, value: value};
	    } else {
	      return {done: true, value: undefined};
	    }
	  }
	}

	// 返回一个新的迭代器，可以从start到stop计数。
	function range(start, stop) {
	  return new RangeIterator(start, stop);
	}
```
[查看代码运行情况](http://codepen.io/anon/pen/NqGgOQ)。

这里的实现类似[Java](http://gafter.blogspot.com/2007/07/internal-versus-external-iterators.html)或[Swift](https://schani.wordpress.com/2014/06/06/generators-in-swift/)中的迭代器，不是很糟糕，但也不是完全没有问题。我们很难说清这段代码中是否有bug，这段代码看起来完全不像我们试图模仿的传统for (;;)循环，迭代器协议迫使我们拆解掉循环部分。

此时此刻你对迭代器可能尚无感觉，他们用起来很酷，但看起来有些难以实现。

你大概不会为了使迭代器更易于构建从而建议我们为JS语言引入一个离奇古怪又野蛮的新型控制流结构，但是既然我们有生成器，是否可以在这里应用它们呢？一起尝试一下：

```javascript

	function* range(start, stop) {
	  for (var i = start; i < stop; i++)
	    yield i;
	}
```
[查看代码运行情况](http://codepen.io/anon/pen/mJewga)。

以上4行代码实现的生成器完全可以替代之前引入了一整个RangeIterator类的23行代码的实现。可行的原因是：生成器是迭代器。所有的生成器都有内建`.next()`和`[Symbol.iterator]()`方法的实现。你只须编写循环部分的行为。

我们都非常讨厌被迫用被动语态写一封很长的邮件，不借助生成器实现迭代器的过程与之类似，令人痛苦不堪。当你的语言不再简练，说出的话就会变得难以理解。`RangeIterator`的实现代码很长并且非常奇怪，因为你需要在不借助循环语法的前提下为它添加循环功能的描述。所以生成器是最好的解决方案！

## 我们如何发挥作为迭代器的生成器所产生的最大效力？

 1. 使任意对象可迭代。编写生成器函数遍历这个对象，运行时yield每一个值。然后将这个生成器函数作为这个对象的`[Symbol.iterator]`方法。

 2. 简化数组构建函数。假设你有一个函数，每次调用的时候返回一个数组结果，就像这样：

```javascript

	// 拆分一维数组icons
	// 根据长度rowLength
	function splitIntoRows(icons, rowLength) {
	  var rows = [];
	  for (var i = 0; i < icons.length; i += rowLength) {
	    rows.push(icons.slice(i, i + rowLength));
	  }
	  return rows;
	}
```

使用生成器创建的代码相对较短：

```javascript

	function* splitIntoRows(icons, rowLength) {
	  for (var i = 0; i < icons.length; i += rowLength) {
	    yield icons.slice(i, i + rowLength);
	  }
	}
```

行为上唯一的不同是，传统写法立即计算所有结果并返回一个数组类型的结果，使用生成器则返回一个迭代器，每次根据需要逐一地计算结果。

- 获取异常尺寸的结果。你无法构建一个无限大的数组，但是你可以返回一个可以生成一个永无止境的序列的生成器，每次调用可以从中取任意数量的值。

- 重构复杂循环。你是否写过又丑又大的函数？你是否愿意将其拆分为两个更简单的部分？现在，你的重构工具箱里有了新的利刃——生成器。当你面对一个复杂的循环时，你可以拆分出生成数据的代码，将其转换为独立的生成器函数，然后使用`for (var data of myNewGenerator(args))`遍历我们所需的数据。

- 构建与迭代相关的工具。ES6不提供用来过滤、映射以及针对任意可迭代数据集进行特殊操作的扩展库。借助生成器，我们只须写几行代码就可以实现类似的工具。

举个例子，假设你需要一个等效于`Array.prototype.filter`并且支持`DOM NodeLists`的方法，可以这样写：
```javascript

	function* filter(test, iterable) {
	  for (var item of iterable) {
	    if (test(item))
	      yield item;
	  }
	}
```
你看，生成器魔力四射！借助它们的力量可以非常轻松地实现自定义迭代器，记住，**迭代器贯穿ES6的始终**，它是数据和循环的新标准。

以上只是生成器的冰山一角，最重要的功能请继续观看！

## 生成器和异步代码

这是我在一段时间以前写的一些JS代码
```javascript

	         };
	        })
	      });
	    });
	  });
	});
```
可能你已经在自己的代码中见过类似的片段，[异步API](http://www.html5rocks.com/en/tutorials/async/deferred/)通常需要一个回调函数，这意味着你需要为每一次任务执行编写额外的异步函数。所以如果你有一段代码需要完成三个任务，你将看到类似的三层级缩进的代码，而非简单的三行代码。

后来我就这样写了：
```javascript

	}).on('close', function () {
	  done(undefined, undefined);
	}).on('error', function (error) {
	  done(error);
	});
```

异步API拥有错误处理规则，不支持异常处理。不同的API有不同的规则，大多数的错误规则是默认的；在有些API里，甚至连成功提示都是默认的。

这些是到目前为止我们为异步编程所付出的代价，我们正慢慢开始接受异步代码不如等效同步代码美观又简洁的这个事实。

生成器为你提供了避免以上问题的新思路。

实验性的[Q.async()](https://github.com/kriskowal/q/tree/v1/examples/async-generators)尝试结合`promises`使用生成器产生异步代码的等效同步代码。举个例子：
```javascript

	// 制造一些噪音的同步代码。
	function makeNoise() {
	  shake();
	  rattle();
	  roll();
	}

	// 制造一些噪音的异步代码。
	// 返回一个Promise对象
	// 当我们制造完噪音的时候会变为resolved
	function makeNoise_async() {
	  return Q.async(function* () {
	    yield shake_async();
	    yield rattle_async();
	    yield roll_async();
	  });
	}
```
*二者主要的区别是*，异步版本必须在每次调用异步函数的地方添加yield关键字。

在`Q.async`版本中添加一个类似`if`语句的判断或`try/catch`块，如同向同步版本中添加类似功能一样简单。与其它异步代码编写方法相比，这种方法更自然，不像是学一门新语言一样辛苦。

如果你已经看到这里，你可以试着阅读来自James Long 的[更深入地讲解生成器的文章](http://jlongster.com/A-Study-on-Solving-Callbacks-with-JavaScript-Generators)。

生成器为我们提供了一个新的异步编程模型思路，这种方法更适合人类的大脑。相关工作正在不断展开。此外，更好的语法或许会有帮助，[ES7](https://github.com/tc39/ecma262)中有一个[有关异步函数的提案](https://github.com/lukehoban/ecmascript-asyncawait)，它基于promises和生成器构建，并从C#相似的特性中汲取了大量灵感。

## 如何应用这些疯狂的新特性？

在服务器端，现在你可以在io.js中使用ES6（在Node中你需要使用--harmony这个命令行选项）。

在浏览器端，到目前为止只有Firefox 27+和Chrome 39+支持了ES6生成器。如果要在web端使用生成器，你需要使用[Babel](http://babeljs.io/)或[Traceur](https://github.com/google/traceur-compiler#what-is-traceur)来将你的ES6代码转译为Web友好的ES5。

起初，JS中的生成器由Brendan Eich实现，他的设计参考了[Python生成器](https://www.python.org/dev/peps/pep-0255/)，而此Python生成器则受到[Icon](http://www.cs.arizona.edu/icon/)的启发。他们[早在2006年](https://developer.mozilla.org/en-US/docs/Web/JavaScript/New_in_JavaScript/1.7)就在Firefox 2.0中移植了相关代码。但是，标准化的道路崎岖不平，相关语法和行为都在原先的基础上有所改动。Firefox和Chrome中的ES6生成器都是由编译器hacker [Andy Wingo](http://wingolog.org/)实现的。这项工作由[彭博](http://www.bloomberg.com/)赞助支持（没听错，就是大名鼎鼎的那个彭博！）。

## yield;

生成器还有更多未提及的特性，例如：`.throw()`和`.return()`方法、可选参数`.next()`、`yield*`表达式语法。

{done:true}


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/842863894.png