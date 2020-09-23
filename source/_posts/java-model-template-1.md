---
title: Java模板模式（Template模式）
date: 2014-10-15 13:35:00
updated: 2014-11-03 13:36:37
tags: 
- javamodel
- bridge
categories: 
- model

---
Template模式定义：定义一个操作中算法的骨架，将一些步骤的执行延迟到其子类中。

其实Java的抽象类本来就是Template模式，因此使用很普遍。而且很容易理解和使用，我们直接以示例开始：


<!--more-->


    public abstract class Benchmark
    {
    　　/**
    　　* 下面操作是我们希望在子类中完成
    　　*/
    　　public abstract void benchmark();
    
    　　/**
    　　* 重复执行benchmark次数
    　　*/
    　　public final long repeat (int count) {
    　　　　if (count <= 0)
    　　　　　　return 0;
    　　　　else {
    　　　　　　long startTime = System.currentTimeMillis();
    　　　　        for (int i = 0; i < count; i++)
    　　　　　　    benchmark();
    　　　　            long stopTime = System.currentTimeMillis();
    　　　　            return stopTime - startTime;
    　　        }
            }
    }
在上例中，我们希望重复执行benchmark()操作，但是对benchmark()的具体内容没有说明，而是延迟到其子类中描述：

    public class MethodBenchmark extends Benchmark
    {
    　　/**
    　　* 真正定义benchmark内容
    　　*/
    　　public void benchmark() {
    　　　　for (int i = 0; i < Integer.MAX_VALUE; i++){
    　　　 　　System.out.printtln("i="+i);　 　　
    　　　    }
    　　}
    }


至此，Template模式已经完成，是不是很简单？看看如何使用：
   

     Benchmark operation = new MethodBenchmark();
        long duration = operation.repeat(Integer.parseInt(args[0].trim()));
        System.out.println("The operation took " + duration + " milliseconds");

也许你以前还疑惑抽象类有什么用，现在你应该彻底明白了吧？至于这样做的好处，很显然啊，扩展性强，以后Benchmark内容变化，我只要再做一个继承子类就可以，不必修改其他应用代码。

