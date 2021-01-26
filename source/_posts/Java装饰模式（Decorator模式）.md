---
title: "Java装饰模式（Decorator模式）"
categories: [ "Design Patterns" ]
tags: [ "javamodel","decorator" ]
draft: false
slug: "java-model-decorator"
date: "2014-10-22 13:43:00"
---

Decorator常被翻译成"装饰"，我觉得翻译成"油漆工"更形象点，油漆工(decorator)是用来刷油漆的，那么被刷油漆的对象我们称decoratee。这两种实体在Decorator模式中是必须的。

Decorator定义：动态给一个对象添加一些额外的职责，就象在墙上刷油漆。使用Decorator模式相比用生成子类方式达到功能的扩充显得更为灵活。


<!--more-->


##为什么使用Decorator

我们通常可以使用继承来实现功能的拓展，如果这些需要拓展的功能的种类很繁多，那么势必生成很多子类，增加系统的复杂性，同时，使用继承实现功能拓展，我们必须可预见这些拓展功能，这些功能是编译时就确定了，是静态的。

使用Decorator的理由是：这些功能需要由用户动态决定加入的方式和时机。Decorator提供了"即插即用"的方法，在运行期间决定何时增加何种功能。
##如何使用装饰模式

举Adapter中的打桩示例，在Adapter中有两种类：方形桩 圆形桩，Adapter模式展示如何综合使用这两个类，在Decorator模式中，我们是要在打桩时增加一些额外功能，比如，挖坑 在桩上钉木板等，不关心如何使用两个不相关的类。

我们先建立一个接口：

    public interface Work{
    　public void insert();
    }

接口Work有一个具体实现：插入方形桩或圆形桩，这两个区别对Decorator是无所谓。我们以插入方形桩为例：

    public class SquarePeg implements Work{
    　public void insert(){
    　　System.out.println("方形桩插入");
    　}
    }

现在有一个应用：需要在桩打入前，挖坑，在打入后，在桩上钉木板，这些额外的功能是动态，可能随意增加调整修改，比如，可能又需要在打桩之后钉架子(只是比喻)。

那么我们使用Decorator模式，这里方形桩SquarePeg是decoratee(被刷油漆者)，我们需要在decoratee上刷些"油漆"，这些油漆就是那些额外的功能。

    public class Decorator implements Work{
    　private Work work;
    　//额外增加的功能被打包在这个List中
    　private ArrayList others = new ArrayList();
    　//在构造器中使用组合new方式,引入Work对象;
    　public Decorator(Work work){
    　　this.work=work;
    　　others.add("挖坑");
    　　others.add("钉木板");
    　}
    　public void insert(){
    　　newMethod();
    　}

　//在新方法中,我们在insert之前增加其他方法,这里次序先后是用户灵活指定的 　　
　

    public void newMethod(){
    　　otherMethod();
    　　work.insert();
    　}
    　public void otherMethod(){
    　　ListIterator listIterator = others.listIterator();
    　　while (listIterator.hasNext()){
    　　　System.out.println(((String)(listIterator.next())) + " 正在进行");
    　　}
    　}
    }

在上例中，我们把挖坑和钉木板都排在了打桩insert前面，这里只是举例说明额外功能次序可以任意安排。

好了，Decorator模式出来了，我们看如何调用：

    Work squarePeg = new SquarePeg();
    Work decorator = new Decorator(squarePeg);
    decorator.insert();

Decorator模式至此完成。

如果你细心，会发现，上面调用类似我们读取文件时的调用：
  

      FileReader fr = new FileReader(filename);
        BufferedReader br = new BufferedReader(fr);

实际上Java 的I/O API就是使用Decorator实现的，I/O变种很多，如果都采取继承方法，将会产生很多子类，显然相当繁琐。
##Jive中的Decorator实现

在论坛系统中，有些特别的字是不能出现在论坛中如"打倒XXX"，我们需要过滤这些"反动"的字体。不让他们出现或者高亮度显示。

在IBM Java专栏中专门谈Jive的文章中，有谈及Jive中ForumMessageFilter.java使用了Decorator模式，其实，该程序并没有真正使用Decorator,而是提示说：针对特别论坛可以设计额外增加的过滤功能，那么就可以重组ForumMessageFilter作为Decorator模式了。

所以，我们在分辨是否真正是Decorator模式，以及会真正使用Decorator模式，一定要把握好Decorator模式的定义，以及其中参与的角色(Decoratee 和Decorator)。
