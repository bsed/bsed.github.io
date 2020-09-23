---
title: Java原型模式（Prototype模式）
date: 2014-10-19 13:39:00
updated: 2014-11-03 13:40:27
tags: 
- css
- overflow
categories: 
- css

---
Prototype模式定义：用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。

Prototype模式允许一个对象再创建另外一个可定制的对象，根本无需知道任何如何创建的细节，工作原理是：通过将一个原型对象传给那个要发动创建的对象，这个要发动创建的对象通过请求原型对象拷贝它们自己来实施创建。


<!--more-->


##如何使用原型模式

因为Java中的提供clone()方法来实现对象的克隆，所以Prototype模式实现一下子变得很简单。以勺子为例：

public abstract class AbstractSpoon implements Cloneable{
　String spoonName;
　public void setSpoonName(String spoonName) {this.spoonName = spoonName;}
　public String getSpoonName() {return this.spoonName;}
　public Object clone(){
　　Object object = null;
　　try {
　　　object = super.clone();
　　} catch (CloneNotSupportedException exception) {
　　　System.err.println("AbstractSpoon is not Cloneable");
　　}
　　　return object;
　}
}

有两个具体实现(ConcretePrototype)：

    public class SoupSpoon extends AbstractSpoon{
    　public SoupSpoon(){
    　　setSpoonName("Soup Spoon");
    　}
    }

    public class SaladSpoon extends AbstractSpoon{
    　public SaladSpoon(){
    　　setSpoonName("Salad Spoon");
    　}
    }

调用Prototype模式很简单：
AbstractSpoon spoon = new SoupSpoon();
AbstractSpoon spoon = new SaladSpoon();

当然也可以结合工厂模式来创建AbstractSpoon实例。

在Java中Prototype模式变成clone()方法的使用，由于Java的纯洁的面向对象特性，使得在Java中使用设计模式变得很自然，两者已经几乎是浑然一体了。这反映在很多模式上，如Interator遍历模式。
