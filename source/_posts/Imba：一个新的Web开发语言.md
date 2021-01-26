---
title: "Imba：一个新的Web开发语言"
categories: [ "JS" ]
tags: [ "js","imba","mvc" ]
draft: false
slug: "imba-a-new-web-development-language"
date: "2015-08-27 18:10:00"
---

![20150827181524.png][1]

> 原文: 
> [http://www.infoq.com/cn/news/2015/08/imba](http://www.infoq.com/cn/news/2015/08/imba)
> 作者: [张天雷](http://www.infoq.com/cn/author/%E5%BC%A0%E5%A4%A9%E9%9B%B7)

学习程序语言是每个程序员的必经之路。可是这个世界上有太多的程序语言，在来不及反应的时候，最近又出现了一门新的编程语言— [Imba](http://imba.io/) 。本文针对它的一些新特征，做一个简单的介绍。

Imba是一种新的Web编程语言，用它编写的代码可以编译成高性能和可读的JavaScript。根据其官网中的介绍，它在语言层次上支持对DOM节点的定义、扩展、继承、实例化和渲染。在一些较复杂的应用程序中，例如 TodoMVC ，通过使用更少的代码和一个更小的库， 其速度超过React20倍以上 。在Imba的官网上，列举了它的几个突出特点。


<!--more-->


    var number = 42
    var opposite = true
    var string = "the answer is {number}"
    var regex = /answer is (\d+)/
    # Functions:
    var square = do |x| x * x
    # Arrays:
    var list = [1, 2, 3, 4, 5]
    # Objects:
    var math =
      square: square
      cube: do |x| x * math.square(x)
      rand: do Math.random
    # Array comprehensions:
    var cubes = (math.cube num for num in list)
    # Implicit calling:
    math.rand.toFixed 2

### 高的可读性
Imba设计的初衷之一是让编译后的代码尽可能地易读。所有缩进和评论都被予以保留。他们让编译后的代码更加易于理解和人性化，正如Imba官网所形容，“Everything is an expression!”。

### 高的兼容性
Imba官网表示，用户可以在Imba与JavaScript自由切换，绝对可以使用Imba开始编写一个项目（或部分），再后来决定继续用编译的JavaScript来代替。

### 类与作用域
就像JavaScript一样，Imba具有清晰的变量声明。这可以让用户细粒度地控制变量可以访问的作用域。在Sublime Text插件中变量被着色。在这方面，Imba也提供了一些比较简单的例子，例如 [Scoping.imba](http://imba.io/#/syntax/scoping.imba) 。通过将鼠标停在一个局部变量上，可以查看该变量的所有引用。

### 标签
Imba对标签的支持，是其最大的特色之一。关于标签使用的介绍文档现在正在完成中。在那之前，用户可以在一些例子中看到标签的使用，例如 [Scroller](http://imba.io/#/examples/scroller.imba) 或 [TodoMVC](http://imba.io/#/examples/todomvc/app.imba) 。

### 循环/推导式
Imba中的循环类似于CoffeeScript和Python中的数组推导式（Array Comprehension）。它们是表达式，并且可以返回和分配值。作为表达式的循环始终返回一个数组（如Array#map），但也有一些额外的强大功能，如Break / Continue（带或不带参数）。

对于一门新的编程语言，Imba引起了用户广泛的兴趣，在Hacker News上，已经有很多用户展开了广泛 [讨论](https://news.ycombinator.com/item?id=10091454) ，有用户将Imba与其他的语言（如React）进行了简单比较，觉得Imba有一个很棒的编程框架，而且运行很快，总体上是一门非常cool的编程语言。如果想与其他用户讨论对Imba的认识和体验，不妨加入他们。

对于Imba具体的性能，可能还需要读者亲身体验。最后附上 [安装](http://imba.io/#/install.imba) 链接。有关安装和编辑器集成的详细信息，可以参阅 [install.imba](http://imba.io/#install.imba) 。可以从GitHub上的实例 [hello-world](https://github.com/somebee/hello-world-imba) 开始。同时如果有任何问题或意见，读者还可以在 [GitHub](https://github.com/somebee) 上留言。


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/431800149.png