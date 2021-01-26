---
title: "Java四种引用类型"
categories: [ "Java" ]
tags: [ "java","reference" ]
draft: false
slug: "java-four-reference-types"
date: "2012-11-19 16:50:00"
---

## 强引用

一般程序中通过`new`创建的对象的引用都是强引用，强引用只有在从根节点不可达的情况下才会被垃圾回收器回收，所以可能产生内存溢出。

## 软引用

使用`SoftReference`创建，弱于强引用，在内存紧张的时候会被回收，不会产生内存溢出。

## 弱引用

使用`WeakReference`创建，弱于软引用，在系统gc时只要发现弱引用直接回收，不会产生内存溢出。

## 虚引用

使用`PhantomReference`创建，最弱的引用类型，随时都可以被垃圾回收器回收，配合`引用队列`使用可以跟踪对象的回收，因此可以将一些资源的释放放在虚引用中执行和记录。


<!--more-->


**代码示例:**

    package com.xf.util;
    
    import java.lang.ref.PhantomReference;
    import java.lang.ref.ReferenceQueue;
    import java.lang.ref.SoftReference;
    import java.lang.ref.WeakReference;
    
    import org.junit.Test;
    
    public class TestReference {
    
    	    public static class User {
    	        private String name;
    	        private int id;
    
    	        public User(String name, int id) {
    	            this.name = name;
    	            this.id = id;
    	        }
    
    	        @Override
    	        public String toString() {
    	            return id + ":" + name;
    	        }
    	    }
    
    	    @Test
    	    public void testReference() throws Exception {
    	        /** 创建强引用对象*/
    	        User u = new User("hello, world!", 1);
    
    	        /**使用强引用对象创建软引用对象*/
    	        SoftReference<User> userSoftReference = new SoftReference<User>(u);
    	        /**使用强引用对象创建弱引用对象*/
    	        WeakReference<User> userWeakReference = new WeakReference<User>(u);
    
    	        /**使用强引用对象创建虚引用对象*/
    	        ReferenceQueue<User> referenceQueue = new ReferenceQueue<User>();
    	        PhantomReference<User> userPhantomReference = new PhantomReference<User>(u, referenceQueue);
    	        /**删除强引用*/
    	        u = null;
    
    	        /**通过软引用获取*/
    	        System.out.println(userSoftReference.get());
    	        /**通过虚引用获取*/
    	        System.out.println(userWeakReference.get());
    	        /**通过虚引用获取*/
    	        System.out.println(userPhantomReference.get());
    	    }
    	}
    	

代码输出结果如下：

> 1:hello, world! 1:hello, world! null

可以看到，无法使用虚引用获取它引用的对象，再者，虚引用配合引用队列可以跟踪对象的回收时间，因此，可以将一些资源的释放操作放置在虚引用中执行和记录.