---
title: Go语言是彻底的面向组合的并发语言
date: 2014-06-11 11:47:00
updated: 2015-01-22 17:47:33
tags: 
- nodejs
- rectjs
- fastboot
categories: 
- js

---
面向组合编程从 AOP 的Mixin，然后到 Ruby 的Traits，直至DCI设计，包括 Scala 的trait的组合设计，这些都有一个共同特点，组合特性是显式的，也就是说要用**专门语法**来声明组合。其实组合设计应该是面向对象设计中很自然的一种方式，也就是说，只要你使用面向对象语言，隐式上你就具备了强大的组合能力，而无需另外用trait这些语法专门实现。
来自[Less is Exponentially More](http://commandcenter.blogspot.de/2012/06/less-is-exponentially-more.html)认为：

If C++ and Java are about type hierarchies and the taxonomy of types, Go is about composition.
如果说C++和Java是关于类型的层次和分类，那么Go是关于组合。


<!--more-->


## 组合 ##
我们看看Go语言申明一个具有属性字段和方法的类型竟然采取完全平等的方式定义，然后将它们组合成一起，假设一个对象Door有一个属性标识当前状态，两个方法打开和关闭，GO代码如下：

    type Door struct {
        opened bool
    }
     
    func (d *Door) Open() {
        d.opened = true
    }
     
    func (d *Door) Close() {
        d.opened = false
    }

这三大行代码分别是定义一个struct(静态结构)和两个动态功能方法Open()和Close()。Go中是没有Java的Class类的概念，因此，我们不能将静态结构和动态行为混合在一个类中，其实一般情况下，将两者分离是有好处的，因为结构一般是不变的，具有不可变性，而需要混合的情况基本是需要状态可变的，这时使用Go的CSP模型(类似Actor模型)即可。Go语言的这种动静分离设计非常巧妙。

Go语言倡导使用组合替代继承，那么组合能否实现多态性呢？继承中多态性是通过多个子类继承父类来实现，组合是如何实现？下面看看Go语言的组合多态性的实现：

    package main
    
    import (
    	"log"
    )
    
    type B struct{  }
    func (b B) foo() {     log.Printf("...") }
    
    type A struct {
        B
    }
    
    func main() {
    	var a A
    	a.foo()
    }

以上代码点按[http://play.golang.org/p/IU7xq62WC-](http://play.golang.org/p/IU7xq62WC-)直接运行。

foo()是B的方法 但是B嵌入了A中，那么其方法可以看成A的方法被直接调用。这虽然很像Java中A extends B，也就是A继承了B。Go语言通过组合实现了Java传统语言中使用继承实现的多态性。

如果上述被嵌入A中的B是一个接口怎么办呢？在Java中如果一个字段是接口，我们需要通过依赖注入或IOC容器将接口的实现子类注入进去，这是我们使用组合方式经常碰到的场景，当然我们一直期待Java能够将依赖注入加入语言机制，从Java9的提前披露设计中我们丝毫看不到这种倾向，不知道那些参与Java规范设计的人是否经常使用Java开发企业应用，基于JVM的Scala的依赖注入也是如此。

假设B是一个接口：

    package main
    
    import (
    	"log"
    )
    
    type B interface {
        foo() 
    }
    
    type A struct {
        B
    }
    
    func (a A) foo() {     log.Printf("...") }
    
    func main() {
    	var a A
    	a.foo()
    }
[http://play.golang.org/p/p3wATT9I2F](http://play.golang.org/p/p3wATT9I2F)
我们为A实现接口B的foo()方法，是不是类似将func (a A) foo()注入到了type A struct中，当然因为这里B是接口，不能使用a.B.foo()调用，而前面一个例子B是一个实体，可以使用a.B.foo()

因为Go语言自然的语言组合能力，我们不必借助额外依赖注入框架实现**组合+注入**了，这大概是我初期最为惊讶的。同时，那些所谓Mixin或trait功能都自然地融合在这种组件实现中了，比如A本来没有方法foo()，Go语言本身将B的foo()编织weaving进入了A。

## 并发 ##
回到文章开始举例Door，Door是一个有状态的对象，但是守护改变其状态的行为却被拆解在外面，通过组合对接进去，我个人认为这其实是一种贫血模型或者失血模型，类似Java的只有setter/getter方法的POJO，而我们在DDD设计中，倡导使用富模型聚合根来实现，通过聚合根守卫状态，而且聚合根之间通过消息事件驱动，Go的CSP模型可以帮助我们实现。

Go 的CSP模型是使用channel 和goroutine 开发并行程序的能力，协程是一种绿色线程，不是真正的操作系统的线程，而是使用操作系统的一个线程进行不断切换使用，类似Node.JS的单线程异步并发原理，这种花销很小的并发能力比多线程并发要更经济。你不能在JVM上实现Actor, 绿色线程和CSP

CSP如下代码:

    package main
    
    import "fmt"
    import "time"
    
    func sleepAndTalk(secs time.Duration, msg string, c chan string) {
        time.Sleep(secs * time.Second)
        c <- msg
    }
    
    func main() {
        c := make(chan string)
    
        go sleepAndTalk(0, "Hello", c)
        go sleepAndTalk(1, "Gophers!", c)
        go sleepAndTalk(2, "What's", c)
        go sleepAndTalk(3, "up?", c)
    
        for i := 0; i < 4; i++ {
            fmt.Printf("%v ", <-c)
        }
    }

[http://play.golang.org/p/vIH6o-o-HB](http://play.golang.org/p/vIH6o-o-HB)
c := make(chan string)是生成一个通道，go标识协程，同时执行。协程与channel的关系类似于Java中线程与队列。多个CSP作为聚合根能够串联起来：
 如图：![go3.png][1]

来源：[http://www.jdon.com/46681](http://www.jdon.com/46681)

  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/401612941.png