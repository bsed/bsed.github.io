---
title: "Java桥模式（Bridge模式）"
categories: [ "Design Patterns" ]
tags: [ "javamodel","bridge" ]
draft: false
slug: "java-model-bridge-1"
date: "2014-10-16 13:36:00"
---

Bridge定义：将抽象和行为划分开来，各自独立，但能动态的结合。
##为什么使用桥模式

通常，当一个抽象类或接口有多个具体实现(concrete subclass)，这些concrete之间关系可能有以下两种：


<!--more-->


这多个具体实现之间恰好是并列的，如前面举例，打桩，有两个concrete class：方形桩和圆形桩；这两个形状上的桩是并列的，没有概念上的重复，那么我们只要使用继承就可以了。
实际应用上，常常有可能在这多个concrete class之间有概念上重叠。那么需要我们把抽象共同部分和行为共同部分各自独立开来，原来是准备放在一个接口里，现在需要设计两个接口，分别放置抽象和行为。

例如，一杯咖啡为例，有中杯和大杯之分，同时还有加奶 不加奶之分。如果用单纯的继承，这四个具体实现(中杯 大杯 加奶 不加奶)之间有概念重叠,因为有中杯加奶，也有中杯不加奶，如果再在中杯这一层再实现两个继承，很显然混乱，扩展性极差。那我们使用Bridge模式来实现它。
##如何实现桥模式

以上面提到的咖啡 为例。我们原来打算只设计一个接口(抽象类)，使用Bridge模式后，我们需要将抽象和行为分开，加奶和不加奶属于行为，我们将它们抽象成一个专门的行为接口。

先看看抽象部分的接口代码：

    public abstract class Coffee{
    　CoffeeImp coffeeImp;
    　public void setCoffeeImp() {
    　　this.CoffeeImp = CoffeeImpSingleton.getTheCoffeImp();
    　}
    　public CoffeeImp getCoffeeImp() {return this.CoffeeImp;}
    　public abstract void pourCoffee();
    }

其中CoffeeImp 是加不加奶的行为接口，看其代码如下：

    public abstract class CoffeeImp{
    　public abstract void pourCoffeeImp();
    }

现在我们有了两个抽象类，下面我们分别对其进行继承,实现concrete class：

    //中杯
    public class MediumCoffee extends Coffee{
    　public MediumCoffee() {setCoffeeImp();}
    　public void pourCoffee(){
    　　CoffeeImp coffeeImp = this.getCoffeeImp();
    　　//我们以重复次数来说明是冲中杯还是大杯 ,重复2次是中杯
    　　for (int i = 0; i < 2; i++){
    　　　coffeeImp.pourCoffeeImp();
    　　}
    　}
    }

    //大杯
    public class SuperSizeCoffee extends Coffee{
    　public SuperSizeCoffee() {setCoffeeImp();}
    　public void pourCoffee(){
    　　CoffeeImp coffeeImp = this.getCoffeeImp();
    　　//我们以重复次数来说明是冲中杯还是大杯 ,重复5次是大杯
    　　for (int i = 0; i < 5; i++){
    　　　coffeeImp.pourCoffeeImp();
    　　}
    　}
    }

上面分别是中杯和大杯的具体实现.下面再对行为CoffeeImp进行继承：
//加奶

    public class MilkCoffeeImp extends CoffeeImp{
    　MilkCoffeeImp() {}
    　public void pourCoffeeImp(){
    　　System.out.println("加了美味的牛奶");
    　}
    }

    //不加奶
    public class FragrantCoffeeImp extends CoffeeImp{
    　FragrantCoffeeImp() {}
    　public void pourCoffeeImp(){
    　　System.out.println("什么也没加,清香");
    　}
    }

Bridge模式的基本框架我们已经搭好了，别忘记定义中还有一句：动态结合，我们现在可以喝到至少四种咖啡：
中杯加奶
中杯不加奶
大杯加奶
大杯不加奶

看看是如何动态结合的，在使用之前，我们做个准备工作，设计一个单态类(Singleton)用来hold当前的CoffeeImp：

    public class CoffeeImpSingleton{
    　private static CoffeeImp coffeeImp;
    　public CoffeeImpSingleton(CoffeeImp coffeeImpIn)
    　{this.coffeeImp = coffeeImpIn;}
    　public static CoffeeImp getTheCoffeeImp(){
    　　return coffeeImp;
    　}
    }

看看中杯加奶 和大杯加奶 是怎么出来的：

    //拿出牛奶
    CoffeeImpSingleton coffeeImpSingleton = new CoffeeImpSingleton(new MilkCoffeeImp());
    
    //中杯加奶
    MediumCoffee mediumCoffee = new MediumCoffee();
    mediumCoffee.pourCoffee();
    
    //大杯加奶
    SuperSizeCoffee superSizeCoffee = new SuperSizeCoffee();
    superSizeCoffee.pourCoffee();

注意：Bridge模式的执行类如CoffeeImp和Coffee是一对一的关系，正确创建CoffeeImp是该模式的关键。
##Bridge模式在EJB中的应用

`EJB中有一个Data Access Object (DAO)模式`，这是将商业逻辑和具体数据资源分开的，因为不同的数据库有不同的数据库操作。将操作不同数据库的行为独立抽象成一个行为接口DAO，如下：
`Business Object (类似Coffee)`
实现一些抽象的商业操作：如寻找一个用户下所有的订单。涉及数据库操作都使用DAOImplementor。
 
`Data Access Object (类似CoffeeImp)`
一些抽象的对数据库资源操作。
 
`DAOImplementor` 如`OrderDAOCS`, `OrderDAOOracle`, OrderDAOSybase(类似MilkCoffeeImp FragrantCoffeeImp)
具体的数据库操作，如"INSERT INTO "等语句，`OrderDAOOracle`是Oracle OrderDAOSybase是Sybase数据库。
 
数据库 (Cloudscape, Oracle, or Sybase database via JDBC API)

