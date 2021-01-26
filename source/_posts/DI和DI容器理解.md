---
title: "DI和DI容器理解"
categories: [ "Java" ]
tags: [ "java","IOC","DI" ]
draft: false
slug: "di-and-di-container"
date: "2011-12-28 12:55:00"
---

> DI - Dependency
> Injection，意为依赖注射（依赖注入）。指组件（COMPONENT：这里可以看作提供某功能的class或class群）之间依赖系统（容器）的注入（injection）而产生关联关系。也被称为控制反转（Ioc：Inversion
> of Control）模式。

	
通俗一点讲，该模式将实现组件间关系从程序内部提到外部容器来管理。

上面所说的容器，便是DI容器。DI容器的作用，简单地说，就是能减少class间的依存关系，降低它们之间的耦合度。

关于耦合度，举几个例来说明一下：
假如，有两个class A和B，在A中需要调用B，有几种方法：


<!--more-->


## 方法一：在A中直接调用B
耦合度：高
调用例：

    public class B {
        public void dosth() {
            //TODO do something
        }
    }
    public class A {
        public void callB() {
            B b = new B();
            b.dosth();
        }
    
    }

*解说：*
A过于依存B，则若B未完成，或B改变了实现方法等，在B未完成之前A则完全不能动作。

## 方法二：将B抽象成接口（interface）和实现类。然后通过容器或其他方法在执行时设置b。

    public interface B {
        public void dosth();
    }
    
    public class BImpl {
        public void dosth() {
            //
        }
    }
    
    //工厂模式
    public class A {
        private B b = BeanFactory.getInstance().getBean(B.class);
        
        public void callB() {
            b.dosth();
        }
    
    }

//工厂模式后面就不加说明了


或者

    //DI模式
    public class A {
        private B b;
        
        public void setB(B b) {
            this.b = b;
        }
        public void callB() {
            b.dosth();
        }
    
    }


调用方法1：

    public class C {
        public static void main(String[]args) {
            B b = new BImpl();
            
            A a = new A();
            a.setB(b);
            a.callB()；
        }
    }

耦合度：较低
*解说：*
A实际上只依存B接口。A与B的实现类之间的依存关系放到C里去了。

调用方法2：

    public class C {
        public static void main(String[]args) {
            B b = DIContainer.getBean(B.class); //B b = DIContainer.getBean("xxxxName");
            
            A a = new A();
            a.setB(b);
            a.callB()；
        }
    }

耦合度：低
*解说：*
该方法其实就是一个简单的DI容器，或者工厂（Factory）模式的调用原型。不直接在代码里对B的实现（BImpl）进行硬编码，而通过容器/工厂在执行时根据配置文件等，动态查找B的实现类，生成B的实例。


先看看DI的几种常用的实现方法：
### 1，setter方法注入

    public class A {
        private B b;
        
        public void setB(B b) {
            this.b = b;
        }
    }

### 2，构造方法（Constructor）注入

    public class A {
        private B b;
        
        public A(B b) {
            this.b = b;
        }
    }

### 3，方法（Method）注入

    public class A {
        private B b;
        
        public void someMethod(B b) {
            this.b = b;
        }
        
        ...
    }

### 4，属性（Field）注入：例如，通过对属性设置@EJB注释，在EJB容器里便可通过属性注入。
注：EJB3里，实现了DI容器的功能。

    public class A {
        @EJB
        protected B b;
        
        ...
    }

## OpenSource的DI容器

比较著名的开放源代码的DI容器有：
范例解说DI和DI容器 <wbr>- <wbr>轻松理解DISpring Framework
范例解说DI和DI容器 <wbr>- <wbr>轻松理解DIPicoContainer
范例解说DI和DI容器 <wbr>- <wbr>轻松理解DIHiveMind
范例解说DI和DI容器 <wbr>- <wbr>轻松理解DISeasar

**DI的利点：**
上面已经提到了，DI的利点在于尽可能的降低组件之间的依存（耦合）度。当然DI的利点不仅只于此，比如一直被人诟病的EJB，从EJB3开始利用了DI大大轻量化EJB的开发。

在JAVA开发里，已经存在很多优秀的Framework，比如Struts，JSF，Hibernate等，在利用这些框架时，怎么样减小框架之间的耦合性，也可能会成为DI容器发展的一个方向和亮点。
