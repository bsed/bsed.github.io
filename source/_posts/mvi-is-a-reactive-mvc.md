---
title: MVI是一种Reactive MVC
date: 2015-01-04 17:12:00
updated: 2015-01-11 17:21:17
tags: 
- twitter
categories: 
- linux

---
MVI是模型-视图-意图(Model-View-Intent)的简称，它是一种Rective MVC与虚拟DOM更新的结合，[Reactive MVC and the Virtual DOM](http://futurice.com/blog/reactive-mvc-and-the-virtual-dom/)一文描述了这种模式。

MVI是一种前端MVC的升级版，通常我们使用React/Flux 组合作为前端Reactive编程实现，但是它们的API和架构并不真的适合交互式的Rective模式，该文做了详细的解释。

如何使得一个单页应用SPA的MVC架构变得Reactive？首先我们必须去除MVC的C控制器，因为在一个交互组件中，并不会有Reactive组件会以命令方式发送命令给其他组件。


<!--more-->


模型会照顾数据的显示和应用的当前状态，它应该输出一个可观察的数据事件，视图应该是订阅这个模型事件，然后渲染模型所对应的界面，渲染输出应当也是被包装为可观察的(由视图组件输出)，视图组件只是一个功能，获得输入的模型事件，然后将输出渲染结果。

因为没有控制器，相当于MV*架构，我们需要一个组件来负责管理用户输入的事件，传统的控制器也是获得用户输入事件，然后基于其做些计算，再调用类似Model.update(value)函数，因为我们要实现Reactive，那么应该选择另外一条路径，模型会观察到“Reactive Controller”需要更新什么，然后模型决定基于“Reactive Controller”的事件自己进行更新。

这个“Reactive Controller”称为意图Intenet。

意图Intent负责将用户输入事件转为模型友好的事件，它应当解释用户将对模型实现的修改，输出用户意图为事件，意图自己并不改变任何事情。

模型观察到意图事件发生后，如果意图并没有破坏模型内部约束，那么就可以根据事件改变其状态数据(banq注：这点和CQRS中命令和事件非常类似)。
![reactivemvc.png][1]

    Model模型
    输入: 来自意图的用户交互事件
    输出: 数据事件.

    View视图
    输入: 来自模型的数据事件
    输出: 模型将要渲染的一个虚拟 DOM， 同时生成用户的输出事件，比如点击, 键盘键入等事件。

    Intent意图
    输入: 来自视图生成用户事件
    输出: 对模型友好的用户交互事件

为了避免循环导入，我们不能在Node.js中使用require，每个组件都有一个observe()函数作为依赖注入其中，举例，view.observe(model)。为了实现内部模型之间的依赖，一个模型需要导入其他模型，并且监听它们的事件，同样也可以适合意图。

### Virtual DOM和DOM ###
在MVI中，视图并不真的渲染页面给用户，实体输出一个Virtual DOM的可观察的VTree，Vtree是virtual-dom 中的一个虚拟DOM元素，视图负责表达DOM应该是怎么样，但是它自己并不直接去修改DOM，委托给virtual-dom 去修改DOM。

virtual-dom改变DOM很简单，它会订阅观察所有你应用视图的VTRee组件，然后转换VTree到实际的DOM元素，它是通过对Vtree前后比较不同点，然后将不同点更新到DOM中。
[MVI案例源码](http://staltz.com/mvi-example)
  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/2724417318.png