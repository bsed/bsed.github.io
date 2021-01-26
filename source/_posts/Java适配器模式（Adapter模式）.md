---
title: "Java适配器模式（Adapter模式）"
categories: [ "Design Patterns" ]
tags: [ "javamodel","adapter" ]
draft: false
slug: "java-model-adapter-1"
date: "2014-10-18 13:37:00"
---

适配器模式定义：将两个不兼容的类纠合在一起使用，属于结构型模式，需要有Adaptee(被适配者)和Adaptor(适配器)两个身份。
为何使用适配器模式

我们经常碰到要将两个没有关系的类组合在一起使用，第一解决方案是：修改各自类的接口，但是如果我们没有源代码，或者，我们不愿意为了一个应用而修改各自的接口。 怎么办?

使用Adapter，在这两种接口之间创建一个混合接口(混血儿)。
如何使用适配器模式

实现Adapter方式，其实"think in Java"的"类再生"一节中已经提到，有两种方式：组合(composition)和继承(inheritance)，


<!--more-->


假设我们要打桩，有两种类：方形桩 圆形桩。

    public class SquarePeg{
    　public void insert(String str){
    　　System.out.println("SquarePeg insert():"+str);
    　}
    }
    
    public class RoundPeg{
    　public void insertIntohole(String msg){
    　　System.out.println("RoundPeg insertIntoHole():"+msg);
        }
    }

现在有一个应用,需要既打方形桩，又打圆形桩。那么我们需要将这两个没有关系的类综合应用，假设RoundPeg我们没有源代码，或源代码我们不想修改，那么我们使用Adapter来实现这个应用：
public class PegAdapter extends SquarePeg{
　private RoundPeg roundPeg;
　public PegAdapter(RoundPeg peg)(this.roundPeg=peg;)
　public void insert(String str){ roundPeg.insertIntoHole(str);}
}
在上面代码中，RoundPeg属于Adaptee，是被适配者。PegAdapter是Adapter，将Adaptee(被适配者RoundPeg)和Target(目标SquarePeg)进行适配。实际上这是将组合方法(composition)和继承(inheritance)方法综合运用。

PegAdapter首先继承SquarePeg，然后使用new的组合生成对象方式，生成RoundPeg的对象roundPeg，再重载父类insert()方法。从这里，你也了解使用new生成对象和使用extends继承生成对象的不同，前者无需对原来的类修改，甚至无需要知道其内部结构和源代码。

如果你有些Java使用的经验，已经发现，这种模式经常使用。
进一步使用

上面的PegAdapter是继承了SquarePeg,如果我们需要两边继承，即继承SquarePeg 又继承RoundPeg，因为Java中不允许多继承，但是我们可以实现(implements)两个接口(interface)：

    public interface IRoundPeg{
    　public void insertIntoHole(String msg);
    }
    public interface ISquarePeg{
    　public void insert(String str);
    }

下面是新的RoundPeg 和SquarePeg, 除了实现接口这一区别，和上面的没什么区别。

    public class SquarePeg implements ISquarePeg{
    　public void insert(String str){
    　　System.out.println("SquarePeg insert():"+str);
    　}
    }
    
    public class RoundPeg implements IRoundPeg{
    　public void insertIntohole(String msg){
    　　System.out.println("RoundPeg insertIntoHole():"+msg);
    　}
    }

下面是新的PegAdapter,叫做two-way adapter：

    public class PegAdapter implements IRoundPeg,ISquarePeg{
    　private RoundPeg roundPeg;
    　private SquarePeg squarePeg;
    
    　// 构造方法
    　public PegAdapter(RoundPeg peg){this.roundPeg=peg;}
    　// 构造方法
    　public PegAdapter(SquarePeg peg)(this.squarePeg=peg;)
    
    　public void insert(String str){ roundPeg.insertIntoHole(str);}
    }

还有一种叫Pluggable Adapters,可以动态的获取几个adapters中一个。使用Reflection技术，可以动态的发现类中的Public方法。
