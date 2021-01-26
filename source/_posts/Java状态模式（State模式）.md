---
title: "Java状态模式（State模式）"
categories: [ "Design Patterns" ]
tags: [ "javamodel","state" ]
draft: false
slug: "java-model-state"
date: "2014-10-23 13:43:00"
---

State的定义：不同的状态，不同的行为；或者说，每个状态有着相应的行为。
##何时使用状态模式

State模式在实际使用中比较多，适合"状态的切换"。因为我们经常会使用If elseif else 进行状态切换， 如果针对状态的这样判断切换反复出现，我们就要联想到是否可以采取State模式了。


<!--more-->


不只是根据状态，也有根据属性。如果某个对象的属性不同，对象的行为就不一样，这点在数据库系统中出现频率比较高，我们经常会在一个数据表的尾部，加上property属性含义的字段，用以标识记录中一些特殊性质的记录，这种属性的改变(切换)又是随时可能发生的，就有可能要使用State。

在实际使用，类似开关一样的状态切换是很多的，但有时并不是那么明显，取决于你的经验和对系统的理解深度。

这里要阐述的是"开关切换状态" 和" 一般的状态判断"是有一些区别的，" 一般的状态判断"也是有 if..elseif结构，例如：

    if (which==1) state="hello";
    　else if (which==2) state="hi";
    　else if (which==3) state="bye";

这是一个 " 一般的状态判断"，state值的不同是根据which变量来决定的，which和state没有关系。如果改成：

    if (state.euqals("bye")) state="hello";
    　else if (state.euqals("hello")) state="hi";
    　else if (state.euqals("hi")) state="bye";

这就是 "开关切换状态"，是将state的状态从"hello"切换到"hi"，再切换到""bye"；在切换到"hello"，好象一个旋转开关，这种状态改变就可以使用State模式了。

如果单纯有上面一种将"hello"-->"hi"-->"bye"-->"hello"这一个方向切换，也不一定需要使用State模式，因为State模式会建立很多子类，复杂化，但是如果又发生另外一个行为：将上面的切换方向反过来切换，或者需要任意切换，就需要State了。

请看下例：

    public class Context{
    　　private Color state=null;
    　　public void push(){
    　　　　//如果当前red状态 就切换到blue
    　　　　if (state==Color.red) state=Color.blue;
    　　　　//如果当前blue状态 就切换到green
    　　　　else if (state==Color.blue) state=Color.green;
    　　　　//如果当前black状态 就切换到red
    　　　　else if (state==Color.black) state=Color.red;
    　　　　//如果当前green状态 就切换到black
    　　　　else if (state==Color.green) state=Color.black;
    　　　　Sample sample=new Sample(state);
    　　　　sample.operate();
    　　}
    　　public void pull(){
    　　　　//与push状态切换正好相反
    　　　　if (state==Color.green) state=Color.blue;
    　　　　else if (state==Color.black) state=Color.green;
    　　　　else if (state==Color.blue) state=Color.red;
    　　　　else if (state==Color.red) state=Color.black;
    　　　　Sample2 sample2=new Sample2(state);
    　　　　sample2.operate();
    　　}
    }

在上例中，我们有两个动作push推和pull拉，这两个开关动作，改变了Context颜色，至此，我们就需要使用State模式优化它。

另外注意：但就上例,state的变化，只是简单的颜色赋值，这个具体行为是很简单的，State适合巨大的具体行为，因此在,就本例，实际使用中也不一定非要使用State模式，这会增加子类的数目，简单的变复杂。

例如：银行帐户，经常会在Open 状态和Close状态间转换。

例如：经典的TcpConnection，Tcp的状态有创建 侦听 关闭三个，并且反复转换，其创建 侦听 关闭的具体行为不是简单一两句就能完成的，适合使用State。

例如：信箱POP帐号，会有四种状态，start HaveUsername Authorized quit，每个状态对应的行为应该是比较大的，适合使用State。

例如：在工具箱挑选不同工具，可以看成在不同工具中切换，适合使用State。如 具体绘图程序，用户可以选择不同工具绘制方框 直线 曲线，这种状态切换可以使用State。
##如何使用状态模式

State需要两种类型实体参与：
state manager 状态管理器，就是开关，如上面例子的Context实际就是一个state manager，在state manager中有对状态的切换动作。
用抽象类或接口实现的父类，不同状态就是继承这个父类的不同子类。
以上面的Context为例，我们要修改它，建立两个类型的实体。

第一步，首先建立一个父类：

    public abstract class State{
    　　public abstract void handlepush(Context c);
    　　public abstract void handlepull(Context c);
    　　public abstract void getcolor();
    }

父类中的方法要对应state manager中的开关行为，在state manager中 本例就是Context中，有两个开关动作push推和pull拉.那么在状态父类中就要有具体处理这两个动作：handlepush() handlepull()；同时还需要一个获取push或pull结果的方法getcolor()。

下面是具体子类的实现：

    public class BlueState extends State{
    　public void handlepush(Context c){
    　　//根据push方法"如果是blue状态的切换到green" ;
    　　c.setState(new GreenState());
    　}
    　public void handlepull(Context c){
    　　 //根据pull方法"如果是blue状态的切换到red" ;
    　　c.setState(new RedState());
    　}
    　public abstract void getcolor(){ return (Color.blue)}
    }

同样，其他状态的子类实现如blue一样。

第二步，要重新改写State manager 也就是本例的Context：

    public class Context{
    　private Sate state=null; //我们将原来的 Color state 改成了新建的State state;
    　//setState是用来改变state的状态 使用setState实现状态的切换
    　pulic void setState(State state){
    　　　　this.state=state;
    　}
    　public void push(){
    　　//状态的切换的细节部分,在本例中是颜色的变化,已经封装在子类的handlepush中实现,这里无需关心
    　　state.handlepush(this);
    　　//因为sample要使用state中的一个切换结果,使用getColor()
    　　Sample sample=new Sample(state.getColor());
    　　sample.operate();
    　}
    　public void pull(){
    　　state.handlepull(this);
    　　Sample2 sample2=new Sample2(state.getColor());
    　　sample2.operate();
    　}
    }

至此，我们也就实现了State的refactorying过程。

以上只是相当简单的一个实例，在实际应用中，handlepush或handelpull的处理是复杂的。
