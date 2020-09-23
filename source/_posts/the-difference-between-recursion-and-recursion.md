---
title: 递归与递推的区别
date: 2016-01-30 15:12:00
updated: 2016-01-31 15:13:28
tags: 
- css
- fonts
categories: 
- css

---
今天在学习递归和动态规划时有点迷糊了，两者无法区别，在网上差了下，总接如下：
首先要清楚，递推就是迭代。

1.递归其实就是利用系统堆栈,实现函数自身调用,或者是相互调用的过程.在通往边界的过程中,都会把单步地址保存下来,知道等出边界,再按照先进后出的进行运算,这正如我们装木桶一样,每一次都只能把东西方在最上面,而取得时候,先放进取的反而最后取出.递归的数据传送也类似.但是递归不能无限的进行下去,必须在一定条件下停止自身调用,因此它的边界值应是明确的.就向我们装木桶一样,我们不能总是无限制的往里装,必须在一定的时候把东取出来.比较简单的递归过程是**阶乘函数**,你可以去看一下.但是递归的运算方法,往往决定了它的效率很低,因为数据要不断的进栈出栈.这时递推便表现出它的作用了,所谓递推,就是免除了数据进出栈的过程.也就是说,不需要函数不断的向边界值靠拢,而直接从边界出发,直到求出函数值.比如,阶乘函数中,递归的数据流动过程如下:  


<!--more-->


`f(3){f(i)=f(i-1)*i}–>f(2)–>f(1)–>f(0){f(0)=1}–>f(1)–>f(2)–f(3){f(3)=6}`

而递推如下:  
`f(0)–>f(1)–>f(2)–>f(3)`

由此可见,递推的效率要高一些,在可能的情况下应尽量使用递推.但是递归作为比较基础的算法,它的作用不能忽视.所以,在把握这两种算法的时候应该特别注意.

2.递归是自顶向下逐步拓展需求，最后自下向顶运算。即由f(n)拓展到f(1)，再由f(1)逐步算回f(n)
迭代是直接自下向顶运算，由f(1)算到f(n)。