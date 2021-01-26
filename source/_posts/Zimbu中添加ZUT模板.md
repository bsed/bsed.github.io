---
title: "Zimbu中添加ZUT模板"
categories: [ "Zimbu" ]
tags: [ "zimbu","ZUT" ]
draft: false
slug: "zimbu-zut-specification"
date: "2015-01-08 20:46:00"
---

Zimbu在T-Dose上的介绍 视频见[youtube](https://www.youtube.com/watch?v=_O-QdG2X1Lw)
![zimbu_cutout.jpg][1]

【更新】

通过Zimbu的模板（ZUT）在你的应用程序中创建一个网页或者UI，你可以用html和css 创建一个网页，然后嵌入zimbu的代码到你的网页中，增加程序的互动性。创建后的模板可以用在任何服务器端和客户端。这意味着可以在服务器端进行渲染。
事件监听很简单的，可以看下在[zutdemo](https://code.google.com/p/zimbu/source/browse/#hg%2Fzutdemo)目录的例子，这个例子虽然不完整，但你可以看下他是怎么工作的，你也可以看官方的Zimbu模板规范[Zimbu Templates Specification](https://zimbu.googlecode.com/hg/spec/zut.html)或Zimbu模块文档[ ZUT module documentation](https://zimbu.googlecode.com/hg/docs/ZUT.html)
Zimbu 现在支持可变参数`varargs`,参数的个数是可变的，这里以format()方法说明，可以将任意数量的参数格式化成字符串。
这一点非常像Python，阅读更多的说明可以看[language spec](https://zimbu.googlecode.com/hg/spec/zimbu.html#Method%32call_Passing%32arguments%32by%32name)
同时，加入了可选参数和命名参数的支持，可以减少用略为不同的参数写入函数的几个版本的代码量，阅读更多请访问： [language spec](https://zimbu.googlecode.com/hg/spec/zimbu.html#Method%32call_Passing%32arguments%32by%32name)

【为是么要用zimbu】

 - 假如你想写一个新的程序，像一个文本编辑器。你会什么语言写的吗？
 - 尽可能快，所以解释语言是。
 - 你不想微内存管理，所以C是。
 - 你不需要编程人员有个度，所以C++是。
 - 你想快速启动和不依赖于一个大运行库，所以Java是。
 - 已运行在大多数系统中，用C编译器什么的，所以D是。

没有现成的语言真的满足了这些要求，让我们创建一个新的吗！

zimbu是一个实验性的编程语言。这是一个非常实用的语言，没有废话的种。它汲取了许多现有的语言的好东西，避免了它们的不足。然后提出了新的编程思路。

【目标】

更多的目标见此页面[Goals page](http://www.zimbu.org/design/goals)

 - 易读取，代码读的速度是写的N倍
 - 避免常见错误，写糟糕的代码是非常困难的（但你可以如果你真的想写的黑客）
 - 保持简短而明确的，不要相同的事情做两次-没有头文件，不要重复类型规格
 - 效果应该是可预见的和不依赖于另一个文件的东西
 - 高效执行：没有启动延迟，合理的内存使用/没有即时编译器的影响，减少“停止世界“垃圾收集。
 - 支持范围广泛的应用程序可以使用zimbu写一个操作系统内核，一个简短的脚本和一个大的GUI应用程序。
 - 便携式可以编译和运行在几乎所有的系统
 - 许多标准的数据类型，模块和类最需要的东西已经在那里了

【选择】

主要选择到目前为止（更多的[Design page](http://www.zimbu.org/design)）：

 - 程序到C的转换和使用C编译器生成的机器代码（也可以是其他的东西后）
 - 还生产JavaScript，所以zimbu代码可以运行在Web浏览器。
 - 主要使用静态类型检查，也支持运行时类型检查
 - 面向对象，所有的数据都是像一个对象处理的，但也有简单的类型
 - 函数式编程的支持，而回调和LAMBDA方法
 - 重要的定义在symbol，这就避免了在大项目名称冲突
 - 标准模块和类都没有重要的声明
 - 许多模块是语言的一部分，不需要第三方库
 - 所有的关键词是大写字母，你可以用其他的名字而不用担心下一个版本的打破你的计划

  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/1150154791.jpg