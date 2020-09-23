---
title: Java高级基础 锁消除(LockEliminate )
date: 2015-08-25 17:12:00
updated: 2015-12-07 17:21:19
tags: 
- java
- threadlocal
categories: 
- java

---
## 概述

`锁消除`是Java虚拟机在JIT编译时，通过对运行上下文的扫描，去除不可能存在共享资源竞争的锁，通过锁消除，可以节省毫无意义的请求锁时间。

## 实验

看如下代码：


<!--more-->


    package com.xf.util;
    
    public class TestLockEliminate {
    	public static String getString(String s1, String s2) {
    		StringBuffer sb = new StringBuffer();
    		sb.append(s1);
    		sb.append(s2);
    		return sb.toString();
    	}
    
    	public static void main(String[] args) {
    		long tsStart = System.currentTimeMillis();
    		for (int i = 0; i < 1000000; i++) {
    			getString("TestLockEliminate ", "Suffix");
    		}
    		System.out.println("一共耗费：" + (System.currentTimeMillis() - tsStart)
    				+ " ms");
    	}
    }

`getString()`方法中的StringBuffer 是方法内的局部变量，作用于方法内部，不可能逃逸出该方法，因此他就不可能被多个线程同时访问，也就没有资源的竞争，但是`StringBuffer`的`append`操作却需要执行同步操作:


    @Override
    public synchronized StringBuffer append(String str) {
        toStringCache = null;
        super.append(str);
        return this;
    }

默认耗费时间：

    一共耗费：471 ms

逃逸分析和锁消除分别可以使用参数`-XX:+DoEscapeAnalysis`和`-XX:+EliminateLocks`(锁消除必须在-server模式下)开启。使用如下参数运行上面的程序：

    -XX:+DoEscapeAnalysis -XX:-EliminateLocks

得到如下结果：

    一共耗费：441 ms

## 结论
由上面的例子可以看出，关闭了锁消除之后，StringBuffer每次append都会进行锁的申请，浪费了不必要的时间，开启锁消除之后性能得到了提高。