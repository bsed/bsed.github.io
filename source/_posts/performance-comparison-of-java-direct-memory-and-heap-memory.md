---
title: Java 直接内存和堆内存的性能比较
date: 2015-04-06 16:09:00
updated: 2015-12-07 16:23:18
tags: 
- java
- executor
- executorservice
categories: 
- java

---
## 背景知识

在JDK 1.4中新加入了NIO（New Input/Output）类，引入了一种基于通道（Channel）与缓冲区（Buffer）的I/O方式，它可以使用*Native函数库直接分配堆外内存*，然后通过一个存储在Java堆里面的`DirectByteBuffer`对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为*避免了在Java堆和Native堆中来回复制数据*。
显然，本机直接内存的分配不会受到Java堆大小的限制，但是，既然是内存，则肯定还是会受到本机总内存（包括RAM及SWAP区或者分页文件）的大小及处理器寻址空间的限制。服务器管理员配置虚拟机参数时，一般会根据实际内存设置-Xmx等参数信息，但经常会忽略掉直接内存，使得各个内存区域的总和大于物理内存限制（包括物理上的和操作系统级的限制），从而导致动态扩展时出现OutOfMemoryError异常。

上面这段话引用自[该文章](http://book.51cto.com/art/201107/278886.htm)

## 代码实验

注意到上面这段话中的黑体字：这样能在一些场景中显著提高性能，因为避免了在*Java堆和Native堆中来回复制数据*，然而这个“一些场景”具体指什么呢？我们通过代码做个实验看看。


<!--more-->


    package com.xf.jvm; 
    import org.junit.Test;
    import java.nio.ByteBuffer;
    public class TestDirectMemory {
        /**
         * 测试DirectMemory和Heap读写速度。
         */
        @Test
        public void testDirectMemoryWriteAndReadSpeed() {
            long tsStart = System.currentTimeMillis();
            ByteBuffer buffer = ByteBuffer.allocateDirect(400);
            for (int i = 0; i < 100000; i++) {
                for (int j = 0; j < 100; j++) {
                    buffer.putInt(j);
                }
                buffer.flip();
                for (byte j = 0; j < 100; j++) {
                    buffer.getInt();
                }
                buffer.clear();
            }
            System.out.println("DirectMemory读写耗用： " + (System.currentTimeMillis() - tsStart) + " ms");
            tsStart = System.currentTimeMillis();
            buffer = ByteBuffer.allocate(400);
            for (int i = 0; i < 100000; i++) {
                for (int j = 0; j < 100; j++) {
                    buffer.putInt(j);
                }
                buffer.flip();
                for (byte j = 0; j < 100; j++) {
                    buffer.getInt();
                }
                buffer.clear();
            }
            System.out.println("Heap读写耗用： " + (System.currentTimeMillis() - tsStart) + " ms");
        }
    
        /**
         * 测试DirectMemory和Heap内存申请速度。
         */
        @Test
        public void testDirectMemoryAllocate() {
            long tsStart = System.currentTimeMillis();
            for (int i = 0; i < 100000; i++) {
                ByteBuffer buffer = ByteBuffer.allocateDirect(400);
    
            }
            System.out.println("DirectMemory申请内存耗用： " + (System.currentTimeMillis() - tsStart) + " ms");
            tsStart = System.currentTimeMillis();
            for (int i = 0; i < 100000; i++) {
                ByteBuffer buffer = ByteBuffer.allocate(400);
            }
            System.out.println("Heap申请内存耗用： " + (System.currentTimeMillis() - tsStart) + " ms");
        }
    }

运行结果见下图:
jdk1.5

    DirectMemory读写耗用： 889 ms
    Heap读写耗用： 2070 ms
    DirectMemory申请内存耗用： 1089 ms
    Heap申请内存耗用： 77 ms

jdk1.8

    DirectMemory读写耗用： 102 ms
    Heap读写耗用： 188 ms
    DirectMemory申请内存耗用： 228 ms
    Heap申请内存耗用： 48 ms

从上面的实验结果可以看出，直接内存在读和写的性能都优于堆内内存，但是内存申请速度却不如堆内内存，所以可以归纳一下： 直接内存适用于不常申请，但是需要频繁读取的场景，在需要频繁申请的场景下不应该使用**直接内存(DirectMemory)**，而应该使用**堆内内存(HeapMemory)**。
