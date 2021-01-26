---
title: "Java8 新特性： Optional 学习"
categories: [ "Java" ]
tags: [ "java","optional" ]
draft: false
slug: "optional-a-new-feature-of-java-8"
date: "2019-01-24 15:43:00"
---

**JDK8常用新特性：**

## 1: Optional

说明：Optional 类是一个可以为null的容器对象，Optional 类的引入很好的解决空指针异常。

### 1.1：OPtional实例的创建


<!--more-->


```java
//生成一个空实例
 Optional<String> empty = Optional.empty();

//构造一个name的Optional
Optional<String> name = Optional.of("name");

//生成一个null
Optional<String> nullOptional = Optional.ofNullable(null);
```

这里可以看出：创建一个空的实例可以用`Optional.empty()`或者`Optional.ofNullable(null)`,创建一个不为空的Optional实例用`of()`或者`ofNullable`，当结果无法确认是否为空的时候使用`ofNullable()`;

### 1.2：访问对象的值 get() 方法

![99626-dhgrr5kmlxg.png](https://imgs.gnux.cn/usr/uploads/2019/01/1498842719.png)

你以为使用了Optional就可以避免`NoSuchElementException`异常，想太多：

![66700-8w2gwh4yr6.png](https://imgs.gnux.cn/usr/uploads/2019/01/1249642033.png)

那么问题来了，如果我们用`ofNullable`存储了一个未知是否为空的对象怎么办====>很快找到了isPresent()方法，点击去可以看到有：

总结：从上可以看出，如果我们生成一个不为空的实例则可以使用of，如果不确认ofNullable，当使用ofNullable时候请注意判断是否为空；

### 1.3 ifPresent()方法

使用isPresent方法的时候我们还看到了ifPresent方法，来玩一下

```java
//生成一个空实例
Optional<String> emty = Optional.empty();
//构造一个name的Optional
Optional<String> name = Optional.of("name");

if (emty.isPresent()){
    System.out.println("不为空");
}else{
    System.out.println("为空");
}
emty.ifPresent(s -> System.out.println("值为：" + s));
name.ifPresent(n -> System.out.println("值为：" + n));
```

结果大家都知道了，嘿嘿

### 1.4 orElse() 方法　

```java
String test = null;
String ss = Optional.ofNullable(test).orElse("hello word");
System.out.println(ss);
```

即如果test不为空则为test，如果没有值则为“hello word”；

### 1.5 *orElseGet()方法*

*orElseGet()方法同orElse方法，当使用orElse()方法的时候，无论设置初始值是否为空都会执行else方法；当使用orElseGet的时候，是注重获取值，当初始不为空则slse不会执行；*

### 1.6 *orElseThrow() 方法*

*即当传入对象为空的时候，抛出异常*

```java
//生成一个null
String test = null;
Optional.ofNullable(test).orElseThrow(() -> new NoSuchElementException());
```

### 1.7  map()

```java
//生成一个null
String test = null;
String email = Optional.ofNullable(test)
.map(n -> n).orElse("ssss");`
```

### 1.8  filter()

```java
Optional<String> result = Optional.ofNullable("name@123")
        .filter(u -> u != null && u.contains("@"));
System.out.println(result.isPresent());   //======>true
```

原文：[https://www.cnblogs.com/code-sayhi/articles/9621792.html](https://www.cnblogs.com/code-sayhi/articles/9621792.html)