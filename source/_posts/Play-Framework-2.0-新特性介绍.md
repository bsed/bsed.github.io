---
title: "Play Framework 2.0 新特性介绍"
categories: [ "Java","SQL" ]
tags: [ "play2" ]
draft: false
slug: "Play-framework2-texing"
date: "2014-11-04 18:55:58"
---

Play Framework 刚刚发布了[2.0正式版][1]，来看看 Play Framework 2.0 的新特性介绍吧。

原生支持 Java 和 Scala
-----------------


<!--more-->


Play Framework 1.x 完全是采用 Java 编写的，可通过插件来支持 Scala，而 2.0 版本已经完全内置对 Scala 的支持，而且 Play 也首推使用 Scala 开发应用，你可以同时使用 Scala 和 Java 来开发 Play 应用。

![1257280.png][2]
**使用 Java 编写的控制器**

![1257281.png][3]
**快速引用开发**

在 1.x 版本中，提供了 “Play feel” 基于控制台的应用发布和基于浏览器的错误报告，而 Play 2.0 大大的扩展了开发者执行代码片段、测试以及在运行应用上下文中执行 SHELL 脚本的能力
![1257282.png][4]

**Pushing Type Safety to a New Level**

受益于静态类型语言，编译器可单独对你的代码中的某一部分进行检查，这也就是为什么 Play 2.0 默认使用基于 Scala 模板引擎编写 Play 应用的原因。这并不是说你必须是精通 Scala 才能办到。但熟悉 Scala 对开发 Play 2.0 应用是很有好处的。

Play 2.0 在进行编译和类型检查更加的深入，Routes (用来定义 URL 和 action 的映射), 模板以及资源可直接编译 (使用 LESS, CoffeeScript 和 Google Closure compiler)，提供了一个统一的开发流程，不管是客户端开发还是服务器端。其结果是更多的错误将在开发阶段暴露出来，加速开发的进程，在多人参与的大项目中也跟易于管理。
![1257283.png][5]
**玩得(Playing) 更爽Playing Nicely**

Play 1.x 通过 Play 特有的运行环境来实现一些技术，例如 Java类属性以及类的热加载。在 Play 2.0 中我们采用更加标准的方法做运行时的发布，使得使用 Scala 编写核心框架并在 SBT 上构建变得可能。

Play 2.0 提供一个跟 Play 1.x 一样简单的 “play new, run, start” 体验，同时具有跟具可扩展性的基础框架。Play 2.0 提供一个预先配置的构建脚本，使得 1.x 的大多数项目无需修改就可以在 2.0 中运行。但如果你希望修改应用程序的构建和发布，你需要根据需要来定制适配器。
![1257284.png][6]
**Pick and Choose**

在最近几年，Web 应用技术发展非常迅速，Play 2.0 提供了一个很先进的架构可以让你非常灵活的进行功能定制。例如你不想使用数据库，那么你可以禁用 DBPlugin 插件；想使用你自己的模板引擎，没问题，用插件。你完全可以根据你的需要来决定 Play 提供的功能。
![1257285.png][7]
在 conf/application.conf 中禁用插件
**透过 Akka 实现灵活的伸缩性**

Play 基于一个轻量级、无状态、Web友好的架构，其特点就是可预测以及最小的资源消耗（包括 CPU、内存和线程），特别适合用来开发高可伸缩性的应用程序。这里部分要感谢 Akka 2.0。 Akka 是一个用 Scala 编写的库，用于简化编写容错的、高可伸缩性的 Java 和 Scala 的 Actor 模型应用。
![1257286.png][8]
在本例中，你可看到 Calculator actor 映射到 Play 的非阻塞方式
![1257287.png][9]
上面你可看到 ChatRoomActor 使用 Comet 发送消息
**高级 I/O 和流处理**

Web 开发一项非常重要的技术就是“推”和非堵塞技术。Play 2.0 使用一个 Iteratee IO 实现用于实现对“推”和非堵塞支持，以及使用内置的 WebSocket 和 Comet 来进行进行文件流处理。
![1257288.png][10]

Ready, Steady, Play!

你是否已经准备好了开始 Play 2.0 之旅呢？可以先看看 Play 2.0 提供的一些[示例程序][11]，然后再做决定 :)

你还可以看看如何开始 [Play 2.0 开发][12]。


  [1]: http://developer.51cto.com/art/201203/323392.htm
  [2]: https://imgs.gnux.cn/usr/uploads/2014/11/3040593173.png
  [3]: https://imgs.gnux.cn/usr/uploads/2014/11/1641465802.png
  [4]: https://imgs.gnux.cn/usr/uploads/2014/11/3775666322.png
  [5]: https://imgs.gnux.cn/usr/uploads/2014/11/1012873009.png
  [6]: https://imgs.gnux.cn/usr/uploads/2014/11/616266671.png
  [7]: https://imgs.gnux.cn/usr/uploads/2014/11/171967429.png
  [8]: https://imgs.gnux.cn/usr/uploads/2014/11/108929072.png
  [9]: https://imgs.gnux.cn/usr/uploads/2014/11/1702962187.png
  [10]: https://imgs.gnux.cn/usr/uploads/2014/11/3024425938.png
  [11]: https://github.com/playframework/Play20/tree/master/samples
  [12]: http://developer.51cto.com/art/201203/323465.htm