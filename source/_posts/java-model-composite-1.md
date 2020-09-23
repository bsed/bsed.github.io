---
title: Java组合模式（Composite模式）
date: 2014-10-25 13:45:00
updated: 2015-01-13 14:18:07
tags: 
- designer
categories: 
- default

---
Composite定义：将对象以树形结构组织起来，以达成“部分－整体” 的层次结构，使得客户端对单个对象和组合对象的使用具有一致性。

Composite比较容易理解，想到Composite就应该想到树形结构图。组合体内这些对象都有共同接口，当组合体一个对象的方法被调用执行时，Composite将遍历(Iterator)整个树形结构，寻找同样包含这个方法的对象并实现调用执行。可以用牵一动百来形容。

所以Composite模式使用到Iterator模式，和Chain of Responsibility模式类似。


<!--more-->


Composite好处：
使客户端调用简单，客户端可以一致的使用组合结构或其中单个对象，用户就不必关心自己处理的是单个对象还是整个组合结构，这就简化了客户端代码。
更容易在组合体内加入对象部件。客户端不必因为加入了新的对象部件而更改代码。
##如何使用Composite

首先定义一个接口或抽象类，这是设计模式通用方式了，其他设计模式对接口内部定义限制不多，Composite却有个规定，那就是要在接口内部定义一个用于访问和管理Composite组合体的对象们（或称部件Component）。

下面的代码是以抽象类定义，一般尽量用接口interface。

    public abstract class Equipment{
    　private String name;
    　//网络价格
    　public abstract double netPrice();
    　//折扣价格
    　public abstract double discountPrice();
    　//增加部件方法　　
    　public boolean add(Equipment equipment) { return false; }
    　//删除部件方法
    　public boolean remove(Equipment equipment) { return false; }
    　//注意这里，这里就提供一种用于访问组合体类的部件方法。
    　public Iterator iter() { return null; }
    　public Equipment(final String name) { this.name=name; }
    }

抽象类Equipment就是Component定义，代表着组合体类的对象们,Equipment中定义几个共同的方法。

    public class Disk extends Equipment{
    　public Disk(String name) { super(name); }
    　//定义Disk网络价格为1
    　public double netPrice() { return 1.; }
    　//定义了disk折扣价格是0.5 对折。
    　public double discountPrice() { return .5; }
    }

Disk是组合体内的一个对象，或称一个部件，这个部件是个单独元素( Primitive)。

还有一种可能是，一个部件也是一个组合体，就是说这个部件下面还有'儿子'，这是树形结构中通常的情况，应该比较容易理解。现在我们先要定义这个组合体：

    abstract class CompositeEquipment extends Equipment{
    　private int i=0;
    　//定义一个Vector 用来存放'儿子'
    　private List equipment=new ArrayList();
    　public CompositeEquipment(String name) { super(name); }
    　public boolean add(Equipment equipment) {
    　　this.equipment.add(equipment);
    　　return true;
    　}
    　public double netPrice(){
    　　double netPrice=0.;
    　　Iterator iter=equipment.iterator();
    　　for(iter.hasNext())
    　　　netPrice+=((Equipment)iter.next()).netPrice();
    　　return netPrice;
    　}
    　public double discountPrice(){
    　　double discountPrice=0.;
    　　Iterator iter=equipment.iterator();
    　　for(iter.hasNext())
    　　　discountPrice+=((Equipment)iter.next()).discountPrice();
    　　return discountPrice;
    　}
    　//注意这里，这里就提供用于访问自己组合体内的部件方法。
    　//上面dIsk 之所以没有，是因为Disk是个单独(Primitive)的元素.
    　public Iterator iter(){
    　　return equipment.iterator() ;
        }
        //重载Iterator方法
    　public boolean hasNext() { return i<equipment.size(); }
    　//重载Iterator方法
    　public Object next(){
    　　if(hasNext())
    　　　return equipment.elementAt(i++);
    　　else
    　　   throw new NoSuchElementException();
    　}
    }

上面CompositeEquipment继承了Equipment，同时为自己里面的对象们提供了外部访问的方法，重载了Iterator，Iterator是Java的Collection的一个接口，是Iterator模式的实现。

我们再看看CompositeEquipment的两个具体类：盘盒Chassis和箱子Cabinet，箱子里面可以放很多东西，如底板，电源盒，硬盘盒等；盘盒里面可以放一些小设备，如硬盘 软驱等。无疑这两个都是属于组合体性质的。

    public class Chassis extends CompositeEquipment{
    　public Chassis(String name) { super(name); }
    　public double netPrice() { return 1.+super.netPrice(); }
    　public double discountPrice() { return .5+super.discountPrice(); }
    }
    
    public class Cabinet extends CompositeEquipment{
    　public Cabinet(String name) { super(name); }
    　public double netPrice() { return 1.+super.netPrice(); }
    　public double discountPrice() { return .5+super.discountPrice(); }
    }

至此我们完成了整个Composite模式的架构。

我们可以看看客户端调用Composote代码：

    Cabinet cabinet=new Cabinet("Tower");
    
    Chassis chassis=new Chassis("PC Chassis");
    //将PC Chassis装到Tower中 (将盘盒装到箱子里)
    cabinet.add(chassis);
    //将一个10GB的硬盘装到 PC Chassis (将硬盘装到盘盒里)
    chassis.add(new Disk("10 GB"));
    
    //调用 netPrice()方法;
    System.out.println("netPrice="+cabinet.netPrice());
    System.out.println("discountPrice="+cabinet.discountPrice());

上面调用的方法netPrice()或discountPrice()，实际上Composite使用Iterator遍历了整个树形结构,寻找同样包含这个方法的对象并实现调用执行。

Composite是个很巧妙体现智慧的模式，在实际应用中，如果碰到树形结构，我们就可以尝试是否可以使用这个模式。

以论坛为例，一个版(forum)中有很多帖子(message),这些帖子有原始贴，有对原始贴的回应贴，是个典型的树形结构，那么当然可以使用Composite模式，那么我们进入Jive中看看，是如何实现的.
##Jive解剖

在Jive中 ForumThread是ForumMessages的容器container(组合体)。也就是说，ForumThread类似我们上例中的 CompositeEquipment.它和messages的关系如下所示：
[thread]
　　 |- [message]
　　 |- [message]
　　 　　 |- [message]
　　 　　 |- [message]
　　 　　 　　 |- [message]

我们在ForumThread看到如下代码：

    public interface ForumThread {
    　....
    　public void addMessage(ForumMessage parentMessage, ForumMessage newMessage)
    　　 throws UnauthorizedException;
    　public void deleteMessage(ForumMessage message)
    　　 throws UnauthorizedException;
    　public Iterator messages();
    　....
    }

类似CompositeEquipment，提供用于访问自己组合体内的部件方法：增加、删除、遍历。

结合我的其他模式中对Jive的分析，我们已经基本大体理解了Jive论坛体系的框架，如果你之前不理解设计模式，而直接去看Jive源代码，你肯定无法看懂。
