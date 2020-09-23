---
title: 深入php多态的实现详解
date: 2014-12-20 14:24:00
updated: 2014-12-20 14:26:52
tags: 
- windows
- vim
- centos6
categories: 
- vim

---
态性是指相同的操作或函数、过程可作用于多种类型的对象上并获得不同的结果。不同的对象，收到同一消息将可以产生不同的结果，这种现象称为多态性。

多态性允许每个对象以适合自身的方式去响应共同的消息。多态性增强了软件的灵活性和重用性。

在面向对象的软件开发中，多态性是最为重要的部分之一。面向对象编程并不只是将相关的方法与数据简单的结合起来，而是采用面向对象编程中的各种要素将现实生活中的各种情况清晰的描述出来。这一小节将对面向对象编程中的多态性作详细的讲解。


<!--more-->


1.什么是多态
-------

多 态（Polymorphism）按字面上意思理解就是“多种形状”。可以理解为多种表现形式，也即“一个对外接口，多个内部实现方法”。在面向对象的理论 中，多态性的一般定义为：同一个操作作用于不同的类的实例，将产生不同的执行结果。也即不同类的对象收到相同的消息时，将得到不同的结果。

在实际的应用开发中，采用面向对象中的多态主要在于可以将不同的子类对象都当作一个父类来处理，并且可以屏蔽不同子类对象之间所存在的差异，写出通用的代码，做出通用的编程，以适应需求的不断变化。

2.多态的应用设计
---------

在实际的应用开发中，通常为了使项目能够在以后的时间里的轻松实现扩展与升级，需要通过继承实现可复用模块进行轻松升级。在进行可复用模块设计时，就需要尽可能的减少使用流程控制语句。此时就可以采用多态实现该类设计。
【示例】例举了通常采用流程控制语句实现不同类的处理。其代码如下所示。 

    <?php
    class painter{ //定义油漆工类
    public function paintbrush(){ //定义油漆工动作
    echo "油漆工正在刷漆！/n";
    }
    }
    class typist{ //定义打字员类
    public function typed(){ //定义打字员工作
    echo "打字员正在打字！/n";
    }
    }
    function printworking($obj){ //定义处理类
    if($obj instanceof painter){ //若对象是油漆工类，则显示油漆工动作
    $obj->paintbrush();
    }elseif($obj instanceof typist){ //若对象是打字员类，则显示打字员动作
    $obj->typed();
    }else{ //若非以上类，则显示出错信息
    echo "Error: 对象错误！";
    }
    }
    printworking(new painter()); //显示员工工作
    printworking(new typist()); //显示员工工作
    ?> 

分析：在上述程序中，首先定义两个员工类：油漆工类和打字员类。然后定义一个处理函数，在该函数中，判断员工是否为已经定义的员工，打印出员工的工作状态。其结果如下所示。 
油漆工正在刷漆 
打字员正在打字
从 以上程序可轻松看出，若想显示其几种员工的工作状态，需要首先定义该员工类，并在该员工类中定义员工的工作，然后在printworking()函数中增 加elseif语句以检查对象是哪一员工类的实例。这在实际的应用中，是非常不可取的。若此时采用多态，则可以轻松解决此问题。

可以首先创建一个员工父类，所有的员工类将继承自该员工父类，并且继承父类的所有方法与属性。然后在员工类中创建“是一”关系，判断是否为合法的员工。

**【示例】例举了采用多态的方式改写上例**。其代码如下所示。

    <?php
    class employee{//定义员工父类
    protected function working(){//定义员工工作，需要在子类的实现
    echo "本方法需要在子类中重载!";
    }
    }
    class painter extends employee{//定义油漆工类
    public function working(){//实现继承的工作方法
    echo "油漆工正在刷漆！/n";
    }
    }
    class typist extends employee{//定义打字员类
    public function working(){
    echo "打字员正在打字！/n";
    }
    }
    class manager extends employee{//定义经理类
    public function working(){
    echo "经理正在开会！";
    }
    }
    function printworking($obj){//定义处理方法
    if($obj instanceof employee){//若是员工对象，则显示其工作状态
    $obj->working();
    }else{//否则显示错误信息
    echo "Error: 对象错误！";
    }
    }
    printworking(new painter());//显示油漆工的工作
    printworking(new typist());//显示打字员的工作
    printworking(new manager());//显示经理的工作
    ?> 
**分析**：在上述程序中，首先定义一个员工基类，并定义一个员工工作状态的方法。然后定义将继承自员工基类的三个员工类：油漆工类、打字员类和经理类。然后定义显示员工工作状态的方法。并在该方法中创建一个“是一”关系，用于判断是否为合法的员工。其结果如下所示。
`油漆工正在刷漆! `
`打字员正在打字! `
`经理正在开会!`
从上例可发现，无论增加多少个员工类，只需要实现自员工父类继承的该员工类和方法。而无须修改显示员工工作状态的方法printworking()。 