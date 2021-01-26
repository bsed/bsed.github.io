---
title: "Java备忘录模式（Memento模式）"
categories: [ "Design Patterns" ]
tags: [ "javamodel","memento" ]
draft: false
slug: "java-model-memento"
date: "2014-10-01 13:14:00"
---

Memento定义：memento是一个保存另外一个对象内部状态拷贝的对象，这样以后就可以将该对象恢复到原先保存的状态。


<!--more-->


Memento模式相对也比较好理解，我们看下列代码：

    public class Originator {
    　　 private int number;
    　　private File file = null;
    　　public Originator(){}
    　　// 创建一个Memento
    　　public Memento getMemento(){
    　　　　return new Memento(this);
    　　}
    　　// 恢复到原始值
    　　public void setMemento(Memento m){
    　　　　 number = m.number;
    　　　　 file = m.file;
    　　}
    }

我们再看看Memento类：

    private class Memento implements java.io.Serializable{
    　　private int number;
    　　private File file = null;
    　　public Memento( Originator o){
    　　　　number = o.number;
    　　　　file = o.file;
    　　}
    }

可见 Memento中保存了Originator中的number和file的值。通过调用Originator中number和file值改变的话，通过调用setMemento()方法可以恢复。

Memento模式的缺点是耗费大，如果内部状态很多，再保存一份，无意要浪费大量内存。
Memento模式在Jsp+Javabean中的应用

在Jsp应用中，我们通常有很多表单要求用户输入，比如用户注册，需要输入姓名和Email等，如果一些表项用户没有填写或者填写错误，我们希望在用户按"提交Submit"后，通过Jsp程序检查，发现确实有未填写项目，则在该项目下红字显示警告或错误，同时，还要显示用户刚才已经输入的表项。

如下图中 First Name是用户已经输入，Last Name没有输入，我们则提示红字警告：
##Java备忘录模式

这种技术的实现，就是利用了Javabean的scope="request"或scope="session"特性，也就是Memento模式。