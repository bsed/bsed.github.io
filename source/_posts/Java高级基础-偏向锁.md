---
title: "Java高级基础 偏向锁"
categories: [ "Java" ]
tags: [ "java","biasedlock" ]
draft: false
slug: "java-advanced-basic-bias-lock"
date: "2015-08-24 17:10:00"
---

## 概述

偏向锁是JDK 1.6提出的一种锁优化方式，其核心思想是如果程序没有竞争，则取消之前已经取得锁的线程的同步操作。也就是说，某一个锁被一个线程获取之后，便进入了偏向锁模式，当该线程再次请求这个锁时，就无需再进行相关的同步操作，从而节省了操作时间。但是如果在此期间，有其他线程申请了这个锁，则退出偏向锁模式。在JVM中可以使用-`XX:+UseBiasedLocking`设置启用偏向锁。

## 实验
看如下的代码:

    package com.xf.util;
    
    import java.util.List;
    import java.util.Vector;
    
    public class TestBiasedLock {
    
    	private static List<Integer> list = new Vector<Integer>();
    
    	public static void main(String[] args) {
    		long tsStart = System.currentTimeMillis();
    		for (int i = 0; i < 10000000; i++) {
    			list.add(i);
    		}
    		System.out.println("一共耗费：" + (System.currentTimeMillis() - tsStart)
    				+ " ms");
    	}
    }


<!--more-->


代码中使用一个循环一直往`Vector`中新增元素，`Vector`的`add`操作会请求锁：
默认不加参数的话：

    一共耗费：5330 ms

我们使用如下的参数运行代码：

    -XX:+UseBiasedLocking
    -XX:BiasedLockingStartupDelay=0
    -Xmx512m
    -Xms512m

`-XX:BiasedLockingStartupDelay=0`这个参数表示虚拟机一启动就启动偏向锁模式，因为默认情况下，虚拟机启动4s之后才会启动偏向锁模式，该例运行时间较短，故做此设置。

得到如下结果：

    一共耗费：782 ms

但是，如果我们使用如下的参数运行程序：

    -XX:-UseBiasedLocking
    -Xmx512m
    -Xms512m

得到的结果如下：

    一共耗费：1356 ms

## 结论

偏向锁在少竞争的情况下，对系统性能有一定的帮助。

## 注意事项

偏向锁在竞争激烈的情况下没有太强的优化效果，因为大量的竞争会导致持有锁的线程不停地切换，锁也很难一直保持偏向模式，此时，使用偏向锁不仅不能优化程序，反而有可能降低程序性能。因为，在竞争激烈的场景下可以使用-`XX:-UseBiasedLocking`参数禁用偏向锁。