---
title: "Java 注解类之常用元注解"
categories: [ "Java" ]
tags: [ "java" ]
draft: false
slug: "common-elements-of-the-java-annotation-class"
date: "2011-10-25 21:44:00"
---

@interface用来声明一个注解，其中的每一个方法实际上是声明了一个**配置参数**。方法的名称就是参数的名称，返回值类型就是参数的类型（返回值类型只能是基本类型、Class、String、enum）。可以通过default来声明参数的默认值。

自定义Java注释类时，我们使用方式如下：

    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    class @interface Author{
        public String name();
        public String company();
    }

这次我们重点讨论修饰注解的注解，也就是我们说的元注解。常用的元注解有四种：`@Retention`,`@Target`,`@Inherited`,`@Documented`

## @Retention

@Retention：表示在什么级别保存该注解信息。

Retention注解有一个属性value，是RetentionPolicy类型的，Enum RetentionPolicy是一个枚举类型， 这个枚举决定了Retention注解应该如何去保持，也可理解为Rentention 搭配 RententionPolicy使用。

RetentionPolicy有3个值：CLASS,RUNTIME,SOURCE


<!--more-->


 - 用@Retention(RetentionPolicy.CLASS)修饰的注解，表示注解的信息被保留在class文件(字节码文件)中当程序编译时，但不会被虚拟机读取在运行的时候；    

 - 用@Retention(RetentionPolicy.SOURCE)修饰的注解,表示注解的信息会被编译器抛弃，不会留在class文件中，注解的信息只会留在源文件中；

 - 用@Retention(RetentionPolicy.RUNTIME)修饰的注解，表示注解的信息被保留在class文件(字节码文件)中当程序编译时，会被虚拟机保留在运行时，
   所以他们可以用反射的方式读取。

RetentionPolicy.RUNTIME 可以让你从JVM中读取Annotation注解的信息，以便在分析程序的时候使用.

## @Target

@Target：表示注解用在什么地方。可能的值在属性ElementType中，它是一个枚举类型。

    ElemenetType.CONSTRUCTOR 构造器声明
    ElemenetType.FIELD 域声明（包括 enum 实例）
    ElemenetType.LOCAL_VARIABLE 局部变量声明
    ElemenetType.METHOD 方法声明
    ElemenetType.PACKAGE 包声明
    ElemenetType.PARAMETER 参数声明
    ElemenetType.TYPE 类，接口（包括注解类型）或enum声明

## @Inherited

@Inherited：表示允许子类继承父类中的注解。

## @Documented

@Documented：表示将此注解包含在 javadoc 中 ，它代表着此注解会被javadoc工具提取成文档。
