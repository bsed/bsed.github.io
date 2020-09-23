---
title: Volatile的陷阱
date: 2014-12-23 11:07:00
updated: 2014-12-23 11:56:59
tags: 
- css
- img
- 图片居中
categories: 
- css

---
最近写的关于在嵌入式开发中常遇到的关于volatile关键字使用的短文，都是些通用的技术，贴上来share。
 
对于volatile关键字，大部分的C语言教材都是一笔带过，并没有做太过深入的分析，所以这里简单整理了一些关于volatile的使用注意事项。实际上从语法上来看volatile和const是一样的，但是如果const用错，几乎不会有什么问题；而volatile用错，后果可能很严重。所以在volatile的使用上，建议大家还是尽量求稳，少用一些没有切实把握的技巧。
注意volatile修饰的是谁
首先来看下面两个定义的区别：


<!--more-->


`uchar * volatile reg;`
这行代码里volatile修饰的是reg这个变量。所以这里实际上是定义了一个uchar类型的指针，并且这个指针变量本身是volatile 的。但是指针所指的内容并不是volatile的！在实际使用的时候，编译器对代码中指针变量reg本身的操作不会进行优化，但是对reg所指的内容 *reg却会作为non-volatile内容处理，对*reg的操作还是会被优化。通常这种写法一般用在对共享指针的声明上，即这个指针变量有可能会被中断等函数修改。将其定义为volatile以后，编译器每次取指针变量的值的时候都会从内存中载入，这样即使这个变量已经被别的程序修改了当前函数用的时候也能得到修改后的值（否则通常只在函数开始取一次放在寄存器里，以后就一直使用寄存器内的副本）。

`volatile uchar *reg;`
这行代码里volatile修饰的是指针所指的内容。所以这里定义了一个uchar类型的指针，并且这个指针指向的是一个volatile的对象。但是指针变量本身并不是volatile的。如果对指针变量reg本身进行计算或者赋值等操作，是可能会被编译器优化的。但是对reg所指向的内容 *reg的引用却禁止编译器优化。因为这个指针所指的是一个volatile的对象，所以编译器必须保证对*reg的操作都不被优化。通常在驱动程序的开发中，对硬件寄存器指针的定义，都应该采用这种形式。

`volatile uchar * volatile reg;`
这样定义出来的指针就本身是个volatile的变量，又指向了volatile的数据内容。

volatile与const的合用
从字面上看，volatile和const似乎是一个对象的两个对立属性，是互斥的。但是实际上，两者是有可能一起修饰同一个对象的。看看下面这行声明：

`extern const volatile unsigned int rt_clock;`
这是在RTOS系统内核中常见的一种声明：rt_clock通常是指系统时钟，它经常被时钟中断进行更新。所以它是volatile，易变的。因此在用的时候，要让编译器每次从内存里面取值。而rt_clock通常只有一个写者（时钟中断），其他地方对其的使用通常都是只读的。所以将其声明为 const，表示这里不应该修改这个变量。所以volatile和const是两个不矛盾的东西，并且一个对象同时具备这两种属性也是有实际意义的。


注意
在上面这个例子里面，要注意声明和定义时对const的使用：

在需要读写rt_clock变量的中断处理程序里面，应该如下定义（define）此变量：

`volatile unsigned int rt_clock;`
而在提供给外部用户使用的头文件里面，可以将此变量声明（declare）为：

`extern const volatile unsigned int rt_clock;`
这样是没有问题的。但是切记一定不能反过来，即定义一个const的变量：

`const unsigned int a;`
但是却声明为非const变量：

`extern unsigned int a;`
这样万一在用户函数里面对a进行了写操作，结果是Undefined。

再看另一个例子：

`volatile struct devregs * const dvp = DEVADDR;`
这里的volatile和const实际上是分别修饰了两个不同的对象：volatile修饰的是指针dvp所指的类型为struct devregs的数据结构，这个结构对应者设备的硬件寄存器，所以是易变的，不能被优化的；而后面的const修饰的是指针变量dvp。因为硬件寄存器的地址是一个常量，所以将这个指针变量定义成const的，不能被修改。

危险的volatile用法
下面将列举几种对volatile的不当使用和可能导致的非预期的结果。

例：定义为volatile的结构体成员

考察下面对一个设备硬件寄存器结构类型的定义：

    struct devregs{ 
        unsigned short volatile csr; 
        unsigned short const volatile data; 
    };

我们的原意是希望声明一个设备的硬件寄存器组。其中有一个16bit的CSR控制/状态寄存器，这个寄存器可以由程序向设备写入控制字，也可以由硬件设备设置反映其工作状态。另外还有一个16bit的DATA数据寄存器，这个寄存器只会由硬件来设置，由程序进行读入。

看起来，这个结构的定义没有什么问题，也相当符合实际情况。但是如果执行下面这样的代码时，会发生什么情况呢？

    struct devregs * const dvp = DEVADDR; 
     
    while ((dvp->csr & (READY | ERROR)) == 0) 
        ; /* NULL - wait till done */

通过一个non-volatile的结构体指针，去访问被定义为volatile的结构体成员，编译器将如何处理？答案是：Undefined！C99 标准没有对编译器在这种情况下的行为做规定。所以编译器有可能正确地将dvp->csr作为volatile的变量来处理，使程序运行正常；也有可能就将dvp->csr作为普通的non-volatile变量来处理，在while当中优化为只有开始的时候取值一次，以后每次循环始终使用第一次取来的值而不再从硬件寄存器里读取，这样上面的代码就有可能陷入死循环！！

如果你使用一个volatile的指针来指向一个非volatile的对象。比如将一个non-volatile的结构体地址赋给一个 volatile的指针，这样对volatile指针所指结构体的使用都会被编译器认为是volatile的，即使原本那个对象没有被声明为 volatile。然而反过来，如果将一个volatile对象的地址赋给一个non-volatile的普通指针，通过这个指针访问volatile对象的结果是undefined，是危险的。

所以对于本例中的代码，我们应该修改成这样：

    struct devregs { 
        unsigned short csr; 
        unsigned short data; 
    }; 
     
    volatile struct devregs * const dvp = DEVADDR;

这样我们才能保证通过dvp指针去访问结构体成员的时候，都是作为volatile来处理的。

例：定义为volatile的结构体类型

考察如下代码：

    volatile struct devregs { 
        /* stuff */ 
    } dev1; 
    ......; 
    struct devregs dev2;

作者的目的也许是希望定义一个volatile的结构体类型，然后顺便定义一个这样的volatile结构体变量dev1。后来又需要一个这种类型的变量，因此又定义了一个dev2。然而，第二次所定义的dev2变量实际上是non-volatile的！！因为实际上在定义结构体类型时的那个 volatile关键字，修饰的是dev1这个变量而不是struct devregs类型的结构体！！

所以这个代码应该改写成这样：

    typedef volatile struct devregs { 
        /* stuff */ 
    } devregs_t; 
     
    devregs_t dev1; 
    ......; 
    devregs_t dev2;

这样我们才能得到两个volatile的结构体变量。

例：多次的间接指针引用

考察如下代码：

    /* DMA buffer descriptor */ 
    struct bd { 
        unsigned int state; 
        unsigned char *data_buff; 
    }; 
     
    struct devregs { 
        unsigned int csr; 
        struct bd *tx_bd; 
        struct bd *rx_bd; 
    }; 
     
    volatile struct devregs * const dvp = DEVADDR; 
     
    /* send buffer */ 
    dvp->tx_bd->state = READY; 
    while ((dvp->tx_bd->state & (EMPTY | ERROR)) == 0) 
        ; /* NULL - wait till done */

这样的代码常用在对一些DMA设备的发送Buffer处理上。通常这些Buffer Descriptor（BD）当中的状态会由硬件进行设置以告诉软件Buffer是否完成发送或接收。但是请注意，上面的代码中对dvp->tx_bd->state的操作实际上是non-volatile的！这样的操作有可能因为编译器对其读取的优化而导致后面陷入死循环。

因为虽然dvp已经被定义为volatile的指针了，但是也只有其指向的devregs结构才属于volatile object的范围。也就是说，将dvp声明为指向volatile数据的指针可以保障其所指的volatile object之内的tx_bd这个结构体成员自身是volatile变量，但是并不能保障这个指针变量所指的数据也是volatile的（因为这个指针并没有被声明为指向volatile数据的指针）。

要让上面的代码正常工作，可以将数据结构的定义修改成这样：

    struct devregs { 
        unsigned int csr; 
        volatile struct bd *tx_bd; 
        volatile struct bd *rx_bd; 
    };

这样可以保证对state成员的处理也是volatile的。不过最为稳妥和清晰的办法还是这样：

    volatile struct devregs * const dvp = DEVADDR; 
    volatile struct bd *tx_bd = dvp->tx_bd; 
     
    tx_bd->state = READY; 
    while ((tx_bd->state & (EMPTY | ERROR)) == 0) 
        ; /* NULL - wait till done */

这样在代码里面能绝对保证数据结构的易变性，即使数据结构里面没有定义好也不会有关系。而且对于日后的维护也有好处：因为这样从代码里一眼就能看出哪些数据结构的访问是必须保证volatile的。

例：到底哪个volatile可能无效

就在你看过前面几个例子，感觉自己可能已经都弄明白了的时候，请看最后这个例子：

    struct hw_bd { 
        ......; 
        volatile unsigned char * volatile buffer; 
    }; 
    
    struct hw_bd *bdp; 
    
    ......; 
    bdp->buffer = ...; (1) 
    bdp->buffer[i] = ...; (2)

请问上面标记了①和②的两行代码，哪个是确实在访问volatile对象，而哪个又是undefined的结果？

答案是：②是volatile的，①是undefined。来看本例的数据结构示意图：

        (non-volatile)
 bdp -->+-------------+
        |             |
        |   ... ...   |
        |             |
        +-------------+    (volatile)   
        |    buffer   |-->+------------+
        +-------------+   |            |
                          |            |
                          |            |
                          +------------+
                          |  buffer[i] |
                          +------------+
                          |            |
                          |            |
                          +------------+
buffer成员本身是通过一个non-volatile的指针bdp访问的，按照C99标准的定义，这就属于undefined的情况，因此对bdp->buffer的访问编译器不一定能保证是volatile的；
虽然buffer成员本身可能不是volatile的变量，但是buffer成员是一个指向volatile对象的指针。因此对buffer成员所指对象的访问编译器可以保证是volatile的，所以bdp->buffer[i]是volatile的。

所以，看似简单的volatile关键字，用起来还是有非常多的讲究在里面的，大家一定要引起重视。
 原文：[http://blog.163.com/zhaojie_ding/blog/static/1729728952007925115019663/](http://blog.163.com/zhaojie_ding/blog/static/1729728952007925115019663/)

另一篇不错的文章：[http://blog.163.com/zhaojie_ding/blog/static/1729728952007925111324379/](http://blog.163.com/zhaojie_ding/blog/static/1729728952007925111324379/)