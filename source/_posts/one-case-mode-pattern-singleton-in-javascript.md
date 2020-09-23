---
title: JavaScript中的单例模式（Singleton Pattern ）
date: 2013-10-06 19:41:00
updated: 2015-10-06 19:46:26
tags: 
- life
categories: 
- default

---
## 单例（Singleton）的定义:
The idea of the singleton pattern is to have only one instance of a specific class. This means that the second time you use the same class to create a new object, you should get the same object that was create the first time.

单例模式是指在程序中一种特殊的类（class），他们仅有一个实例。这意味着当这个类在被第一次实例化后，之后的实例化操作都将获得到第一次实例化时产生的实例。

## 对象字面量与单例

以对象字面量(Object Literal)定义的对象本身即为单例：


<!--more-->


    var o1 = {
        name: “SP1 Object Literal”,
        getName: function() {
            return this.name;
        }
    };
    
    var o2 = {
        name: “SP1 Object Literal”,
        getName: function() {
            return this.name;
        }
    };
    
    // o2的键值与o1完全相同，但他们是不同的对象。
    console.log(o1 === o2); // false


对象字面量实现单例模式的另外一种形式：

    var o = (function(){
        var name = "SP2 IIFE Method";
        var getName = function() {
            return this.name;
        };
        return {
            name: name,
            getName: getName
        };
    }());

## 构造函数与单例

通过new操作符调用构造函数创建对象的方式中，实现单例模式的关键点在于：

 1. 第一次调用构造函数时，存储创建的实例
 2. 后续的调用，直接返回实例

依据这个思路，有三种实现方式。

**第一种**最为简单，但也是最不可靠的方式：将实例存储在全局变量中。

    // 存储实例的全局变量。因为这个全局变量可能被改写，所以此方法不可靠。
    var o = null;
    function SingletonPattern3() {
        if (o) {
            return o;
        }
        this.name = "SP3";
        o = this;
    }
    
    var s1 = new SingletonPattern3();
    var s2 = new SingletonPattern3();
    console.log(s1 === s2); // true

**第二种**方法，将实例存储在构造函数的静态属性中。相对第一种方式，此方式显然会比较安全一些，但此构造函数的静态属性仍然存在被修改的可能性。

    function SingletonPattern4() {
        // 保存构造函数的引用
        var constructor = arguments.callee;
        if (constructor.instance) { 
            return constructor.instance;
        }
        this.name = "SP4";
    
        // 将实例存储于构造函数的静态属性instance中
        constructor.instance = this;
    }
    SingletonPattern4.prototype.getName = function() {
        return this.name;
    };
    
    var s1 = new SingletonPattern4();
    var s2 = new SingletonPattern4();
    console.log(s1 === s2); // true

**第三种**方式，采用闭包。第一次调用之后，存储实例，并改写构造函数自身。

    function SingletonPattern5() {
        var sp = this;
        this.name = "SP5";
    
        // 改写构造函数
        SingletonPattern5 = function() {
            return sp;
        };
    }
    
    SingletonPattern5.prototype.getName = function() {
        return this.name;
    };
    
    var s1 = new SingletonPattern5();
    var s2 = new SingletonPattern5();
    console.log(s1 === s2); // true