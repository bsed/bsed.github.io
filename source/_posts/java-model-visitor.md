---
title: Java访问模式（Visitor者模式）
date: 2014-10-04 13:25:00
updated: 2014-11-03 13:28:23
tags: 
- javamodel
- observer
categories: 
- model

---
Visitor定义：作用于某个对象群中各个对象的操作。它可以使你在不改变这些对象本身的情况下，定义作用于这些对象的新操作。

在Java中，Visitor模式实际上是分离了collection结构中的元素和对这些元素进行操作的行为。
为何使用Visitor模式


<!--more-->


Java的Collection(包括Vector和Hashtable)是我们最经常使用的技术，可是Collection好象是个黑色大染缸，本来有各种鲜明类型特征的对象一旦放入后，再取出时，这些类型就消失了。那么我们势必要用If来判断，如：

    Iterator iterator = collection.iterator()
    while (iterator.hasNext()) {
    　　 Object o = iterator.next();
    　　 if (o instanceof Collection)
    　　 　　 messyPrintCollection((Collection)o);
    　　 else if (o instanceof String)
    　　 　　 System.out.println("'"+o.toString()+"'");
    　　 else if (o instanceof Float)
    　　 　　 System.out.println(o.toString()+"f");
    　　 else
    　　 　　 System.out.println(o.toString());
    }

在上例中，我们使用了 instanceof来判断 o的类型。

很显然，这样做的缺点代码If else if 很繁琐，我们就可以使用Visitor模式解决它。
##如何使用Visitor模式

针对上例，我们设计一个接口visitor访问者：

    public interface Visitor
    {
    　　 public void visitCollection(Collection collection);
    　　 public void visitString(String string);
    　　 public void visitFloat(Float float);
    }

在这个接口中，将我们认为Collection有可能的类的类型放入其中。

有了访问者，我们需要被访问者，被访问者就是我们Collection的每个元素Element，我们要为这些Element定义一个可以接受访问的接口(访问和被访问是互动的，只有访问者，被访问者如果表示不欢迎，访问者就不能访问)。

我们定义这个接口叫Visitable，用来定义一个Accept操作，也就是说让Collection每个元素具备可访问性。

    public interface Visitable{
    　　 public void accept(Visitor visitor);
    }

好了，有了两个接口，我们就要定义他们的具体实现(Concrete class)：

    public class ConcreteElement implements Visitable
    {
    　　 private String value;
    　　 public ConcreteElement(String string) {
    　　 　　 value = string;
    　　 }
    　　 //定义accept的具体内容 这里是很简单的一句调用
    　　 public void accept(Visitor visitor) {
    　　 　　 visitor.visitString(this);
    　　 }
    }

再看看访问者的Concrete实现：

    public class ConcreteVisitor implements Visitor
    {
    　　 //在本方法中,我们实现了对Collection的元素的成功访问
    　　 public void visitCollection(Collection collection) {
    　　 　　 Iterator iterator = collection.iterator()
    　　 　　 while (iterator.hasNext()) {
    　　 　　 　　 Object o = iterator.next();
    　　 　　 　　 if (o instanceof Visitable)
    　　 　　 　　 　　 ((Visitable)o).accept(this);
    　　 　　 }
    
    　　 public void visitString(String string) {
    　　 　　 System.out.println("'"+string+"'");
    　　 }
    
    　　 public void visitFloat(Float float) {
    　　 　　 System.out.println(float.toString()+"f");
    　　 }
    }

在上面的visitCollection我们实现了对Collection每个元素访问，只使用了一个判断语句，只要判断其是否可以访问。

至此，我们完成了Visitor模式基本架构。
##使用Visitor模式的前提

对象群结构中(Collection) 中的对象类型很少改变，也就是说访问者的身份类型很少改变，如上面中Visitor中的类型很少改变，如果需要增加新的操作，比如上例中我们在ConcreteElement具体实现外，还需要新的ConcreteElement2 ConcreteElement3。

可见使用Visitor模式是有前提的，在两个接口Visitor和Visitable中，确保Visitor很少变化，变化的是Visitable，这样使用Visitor最方便。

如果Visitor也经常变化，也就是说，对象群中的对象类型经常改变，一般建议是，不如在这些对象类中逐个定义操作，但是Java的Reflect技术解决了这个问题。