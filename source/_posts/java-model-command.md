---
title: Java命令模式（Command模式）
date: 2014-10-13 13:35:00
updated: 2014-11-03 13:35:57
tags: 
- js
- css
- firefox
categories: 
- css

---
Command模式是最让我疑惑的一个模式，我在阅读了很多代码后，才感觉隐约掌握其大概原理，我认为理解设计模式最主要是掌握起原理构造，这样才对自己实际编程有指导作用。Command模式实际上不是个很具体，规定很多的模式，正是这个灵活性，让人有些confuse。


<!--more-->


##Command定义

不少Command模式的代码都是针对图形界面的，它实际就是菜单命令，我们在一个下拉菜单选择一个命令时，然后会执行一些动作。

将这些命令封装成在一个类中，然后用户(调用者)再对这个类进行操作，这就是Command模式，换句话说，本来用户(调用者)是直接调用这些命令的，如菜单上打开文档(调用者)，就直接指向打开文档的代码，使用Command模式，就是在这两者之间增加一个中间者，将这种直接关系拗断，同时两者之间都隔离,基本没有关系了。

显然这样做的好处是符合封装的特性，降低耦合度，Command是将对行为进行封装的典型模式，Factory是将创建进行封装的模式。

从Command模式，我也发现设计模式一个"通病"：好象喜欢将简单的问题复杂化，喜欢在不同类中增加第三者，当然这样做有利于代码的健壮性 可维护性 还有复用性。
##如何使用命令模式

具体的Command模式代码各式各样，因为如何封装命令，不同系统，有不同的做法。下面事例是将命令封装在一个Collection的List中，任何对象一旦加入List中，实际上装入了一个封闭的黑盒中,对象的特性消失了，只有取出时，才有可能模糊的分辨出。

典型的Command模式需要有一个接口。接口中有一个统一的方法，这就是"将命令/请求封装为对象"：

    public interface Command {
    　　public abstract void execute ( );
    }

具体不同命令/请求代码是实现接口Command，下面有三个具体命令：

    public class Engineer implements Command {
    　　public void execute( ) {
    　　　　//do Engineer's command
    　　}
    }

    public class Programmer implements Command {
    　　public void execute( ) {
    　　　　//do programmer's command
    　　}
    }

    public class Politician implements Command {
    　　public void execute( ) {
    　　　　//do Politician's command
    　　}
    }

按照通常做法，我们就可以直接调用这三个Command，但是使用Command模式，我们要将他们封装起来，扔到黑盒子List里去：

    public class producer{
    　　public static List produceRequests() {
    　　　　List queue = new ArrayList();
    　　　　queue.add( new DomesticEngineer() );
    　　　　queue.add( new Politician() );
    　　　　queue.add( new Programmer() );
    　　　　return queue;
    　　}
    }

这三个命令进入List中后，已经失去了其外表特征，以后再取出，也可能无法分辨出谁是Engineer 谁是Programmer了，看下面如何调用Command模式：

    public class TestCommand {
    　　public static void main(String[] args) {
    　　　　List queue = Producer.produceRequests();
    　　　　for (Iterator it = queue.iterator(); it.hasNext(); )
    　　　　　　　　//取出List中东东,其他特征都不能确定,只能保证一个特征是100%正确,
    　　　　　　　　// 他们至少是接口Command的"儿子".所以强制转换类型为接口Command
    　　　　　　　　((Command)it.next()).execute();
    　　}
    }

由此可见，调用者基本只和接口打交道，不合具体实现交互，这也体现了一个原则，面向接口编程，这样，以后增加第四个具体命令时，就不必修改调用者TestCommand中的代码了。

理解了上面的代码的核心原理，在使用中，就应该各人有自己方法了，特别是在如何分离调用者和具体命令上，有很多实现方法，上面的代码是使用"从List过一遍"的做法。这种做法只是为了演示。

使用Command模式的一个好理由还因为它能实现Undo功能，每个具体命令都可以记住它刚刚执行的动作，并且在需要时恢复。

Command模式在界面设计中应用广泛。Java的Swing中菜单命令都是使用Command模式，由于Java在界面设计的性能上还有欠缺，因此界面设计具体代码我们就不讨论，网络上有很多这样的示例。
