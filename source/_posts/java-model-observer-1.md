---
title: Java观察者模式（Observer模式）
date: 2014-10-09 13:29:00
updated: 2015-01-13 10:28:47
tags: 
- javamodel
- facade
categories: 
- model

---
ava深入到一定程度，就不可避免的碰到设计模式(design pattern)这一概念，了解设计模式，将使自己对java中的接口或抽象类应用有更深的理解。设计模式在java的中型系统中应用广泛，遵循一定的编程模式，才能使自己的代码便于理解，易于交流，Observer(观察者)模式是比较常用的一个模式，尤其在界面设计中应用广泛，而本教程所关注的是Java在电子商务系统中应用，因此想从电子商务实例中分析Observer的应用。


<!--more-->


虽然网上商店形式多样，每个站点有自己的特色，但也有其一般的共性，单就"商品的变化，以便及时通知订户"这一点，是很多网上商店共有的模式，这一模式类似Observer patern。

具体的说，如果网上商店中商品在名称 价格等方面有变化，如果系统能自动通知会员，将是网上商店区别传统商店的一大特色。这就需要在商品product中加入Observer这样角色，以便product细节发生变化时，Observer能自动观察到这种变化，并能进行及时的update或notify动作。
![8-13030220253J16.png][1]
Java观察者模式

Java的API还为为我们提供现成的Observer接口Java.util.Observer。我们只要直接使用它就可以。

我们必须extends Java.util.Observer才能真正使用它：
提供Add/Delete observer的方法；
提供通知(notisfy) 所有observer的方法。
//产品类 可供Jsp直接使用UseBean调用 该类主要执行产品数据库插入 更新

    public class product extends Observable{
    　　private String name;
    　　private float price;
    　　public String getName(){ return name;}
    　　public void setName(){
    　　        this.name=name;
    　　        //设置变化点
    　　        setChanged();
    　　        notifyObservers(name);
    　　}　　　
    　　public float getPrice(){ return price;}
    　　public void setPrice(){
    　　        this.price=price;
    　　        //设置变化点
    　　        setChanged();
    　　        notifyObservers(new Float(price));
    　　}
    　　//以下可以是数据库更新 插入命令.
    　　public void saveToDb(){
    　　        .....................
            }
    }

我们注意到，在product类中 的setXXX方法中，我们设置了 notify(通知)方法，当Jsp表单调用setXXX，实际上就触发了notisfyObservers方法，这将通知相应观察者应该采取行动了。

下面看看这些观察者的代码，他们究竟采取了什么行动：
//观察者NameObserver主要用来对产品名称(name)进行观察的

    public class NameObserver implements Observer{
    　　private String name=null;
    　　public void update(Observable obj,Object arg){
    　　　　if (arg instanceof String){
    　　　　 name=(String)arg;
    　　　　 //产品名称改变值在name中
    　　　　 System.out.println("NameObserver :name changet to "+name);
    　　　　}
    　　}
    }

//观察者PriceObserver主要用来对产品价格(price)进行观察的

    public class PriceObserver implements Observer{
    　　private float price=0;
    　　public void update(Observable obj,Object arg){
    　　　　if (arg instanceof Float){
    　　　　 price=((Float)arg).floatValue();
    　　　　 System.out.println("PriceObserver :price changet to "+price);
    　　　　}
    　　}
    }

Jsp中我们可以来正式执行这段观察者程序：

    <jsp:useBean id="product" scope="session" class="Product" />
    <jsp:setProperty name="product" property="*" />
    
    <jsp:useBean id="nameobs" scope="session" class="NameObserver" />
    <jsp:setProperty name="product" property="*" />
    
    <jsp:useBean id="priceobs" scope="session" class="PriceObserver" />
    <jsp:setProperty name="product" property="*" />
    
    <%
    if (request.getParameter("save")!=null)
    {
    　　product.saveToDb();
    　　out.println("产品数据变动 保存! 并已经自动通知客户");
    }else{
    　　//加入观察者
    　　product.addObserver(nameobs);
    　　product.addObserver(priceobs);
    %>
    　　//request.getRequestURI()是产生本jsp的程序名,就是自己调用自己
    　　<form action="<%=request.getRequestURI()%>" method=post>
    　　<input type=hidden name="save" value="1">
    　　产品名称:<input type=text name="name" >
    　　产品价格:<input type=text name="price">
    　　<input type=submit>
    　　</form>
    <%
    }
    %>

执行改Jsp程序，会出现一个表单录入界面，需要输入产品名称 产品价格，点按Submit后，还是执行该jsp的if (request.getParameter("save")!=null)之间的代码。

由于这里使用了数据javabeans的自动赋值概念，实际程序自动执行了setName setPrice语句。你会在服务器控制台中发现下面信息：
    NameObserver :name changet to ?????(Jsp表单中输入的产品名称)
    PriceObserver :price changet to ???(Jsp表单中输入的产品价格);

这说明观察者已经在行动了。

同时你会在执行jsp的浏览器端得到信息：
    产品数据变动 保存! 并已经自动通知客户

上文由于使用jsp概念，隐含很多自动动作，现将调用观察者的Java代码写如下：

    public class Test {
    　　public static void main(String args[]){
    　　　　Product product=new Product();
    　　　　NameObserver nameobs=new NameObserver();
    　　　　PriceObserver priceobs=new PriceObserver();
    
    　　　　//加入观察者
    　　　　product.addObserver(nameobs);
    　　　　product.addObserver(priceobs);
    
    　　　　product.setName("橘子红了");
    　　　　product.setPrice(9.22f);
    　　}
    }

你会在发现下面信息：
    NameObserver :name changet to 橘子红了
    PriceObserver :price changet to 9.22

这说明观察者在行动了。


  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/1947238787.png