---
title: Java垃圾回收算法概述
date: 2012-12-09 16:02:00
updated: 2015-12-07 16:02:50
tags: 
- java
- threadfactory
- concurrent
categories: 
- java

---
**引用计数**：一个对象被引用计数器加一，取消引用计数器减一，引用计数器为0才能被回收。优点：简单。缺点：不能解决循环引用的问题，比如A引用B，B引用A，但是这两个对象没有被其他任何对象引用，属于垃圾对象，却不能回收；每次引用都会附件一个加减法，影响性能。

**标记清除法**：分为两个阶段：标记阶段和清除阶段。标记阶段通过根节点标记所有*可达*对象，清除阶段清除所有不可达对象。缺点：因为清除不可达对象之后剩余的内存不连续，会产生大量内存碎片，*不利于大对象的分配*。

**复制算法**：将内存空间分成相同的两块，每次只是用其中的一块，垃圾回收时，将正在使用的内存中的存活对象复制到另外一块空间，然后清除正在使用的内存空间中的所有对象，这种回收算法适用于*新生代垃圾回收*。优点：垃圾回收对象比较多时需要复制的对象很少，性能较好；不会存在内存碎片。缺点：将系统内存折半。


<!--more-->


**标记压缩算法**：是一种老年代回收算法，在标记清除的基础上做了一些优化，首先从根节点开始标记所有不可达的对象，然后将所有可达的对象移动到内存的一端，最后清除所有不可达的对象。优点：*不用将内存分为两块；不会产生内存碎片*。

**分代算法**：新生代使用复制算法，老生带使用标记清除算法或者标记压缩算法。几乎所有的垃圾回收期都区分新生代和老生带。

**分区算法**：将整个堆空间分成很多个连续的不同的小空间，每个小空间独立使用，独立回收。为了更好的控制gc停顿时间，可以根据目标停顿时间合理地回收若干个小区间，而不是整个堆空间，从而减少gc停顿时间。