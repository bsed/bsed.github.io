---
title: Java Buffer中的flip()方法
date: 2011-12-30 16:54:00
updated: 2015-12-07 17:41:29
tags: 
- java
- concurrent
- future
categories: 
- java

---
此方法位于：`java.nio.Buffer` Buffer 是一个用于特定基本类型数据的容器。 
缓冲区是特定基本类型元素的线性有限序列。除内容外，缓冲区的基本属性还包括容量（capacity）、大小（limit）和位置（position）：

**缓冲区的capacity** 是它所能容纳的元素的数量。缓冲区的capacity不能为负并且不能更改。
**缓冲区的limit** 是第一个不应该读取或写入的元素的索引。缓冲区的limit不能为负，并且不能大于其capacity。
**缓冲区的position** 是下一个要读取或写入的元素的索引。缓冲区的位置不能为负，并且不能大于其limit。对于每个非boolean基本类型，此类都有一个子类与之对应。
 
在使用缓冲区进行输入输出数据之前，必须确定缓冲区的position，limit都已经设置了正确的值。
 


<!--more-->


如果现在想用这个缓冲区进行*信道*的写操作，由于`write()`方法将从`position`指示的位置开始读取数据，在limit指示的位置停止，因此在进行写操作前，先要将limit的值设为position的当前值，再将position的值设为0。这个操作可以通过这个`flip()`方法实现。

flip()使缓冲区为一系列新的通道写入或相对获取 操作做好准备：它将限制设置为当前位置，然后将位置设置为0，即上边的要求。
 
所以，上边的操作步骤为：`buffer.flip()`;
`out.write(buffer)`;
