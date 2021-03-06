---
title: "Angular的控制器 [Ionic Angularjs 学习笔记]"
categories: [ "JS" ]
tags: [ "angularjs","ionic" ]
draft: false
slug: "ionic-controller-angularjs-angular-learning-notes"
date: "2015-09-02 20:55:00"
---

## 控制器的说明

控制器在AngularJS中的作用是增强视图。在Hello World的例子中,我们并没有使用普通的控 制器,而是使用了一个隐式控制器。

AngularJS中的控制器是一个函数,用来向视图的作用域中添加额外的功能。我们用它来给 作用域对象设置初始状态,并添加自定义行为。

当我们在页面上创建一个新的控制器时,AngularJS会生成并传递一个新的$scope给这个控 制器。可以在这个控制器里初始化$scope。由于AngularJS会负责处理控制器的实例化过程,我 们只需编写构造函数即可。


<!--more-->


## 控制器嵌套(作用域包含作用域)
AngularJS应用的任何一个部分,无论它渲染在哪个上下文中,都有父级作用域存在。对于
ng-app所处的层级来讲,它的父级作用域就是$rootScope。
 - 这里是列表文本有一个例外:在指令内部创建的作用域被称作孤立作用域。

除了孤立作用域外,所有的作用域都通过原型继承而来,也就是说它们都可以访问父级作用 域。如果熟悉面向对象编程,对这个机制应该不会陌生。
默认情况下,AngularJS在当前作用域中无法找到某个属性时,便会在父级作用域中进行查 找。如果AngularJS找不到对应的属性,会顺着父级作用域一直向上寻找,直到抵达$rootScope 为止。如果在$rootScope中也找不到,程序会继续运行,但视图无法更新。