---
title: "golang中 gosched()的作用[go学习笔记]"
categories: [ "Golang" ]
tags: [ "golang" ]
draft: false
slug: "the-role-of-gosched-in-go-golang-learning-notes"
date: "2013-09-16 18:56:00"
---

runtime.Gosched()用于让出CPU时间片。有点像接力赛，，A跑了一会碰到runtime.Gosched()就把接力棒交给B了，A歇着，B继续跑。

    package main
    
    import (
    	"fmt"
    	"runtime"
    )
    
    func say(s string) {
    	for i := 0; i < 2; i++ {
    		runtime.Gosched()
    		fmt.Println(s)
    	}
    }
    func main() {
    	go say("world")
    	say("hello")

<!--more-->


输出：

    hello
    
    world

hello

注意：

1、先输出了hello，后输出了world

2、hello输出了2个，world输出了1个（因为第2个hello输出完，主线程就退出了，第2个world没机会了）

如果把代码中的runtime.Gosched()注释掉，执行结果为：

hello

hello

因为say("hello")这句占用了时间，等它执行完，线程也结束了，say("world")就没有机会了。

从这里也可以看出，goroutine并不是真正的“并行”，goroutine都在一个线程里，它们之间通过不停的让出时间片轮流运行，达到类似运行的效果。

还需要记住一句话：

当一个goroutine发生阻塞，go会自动地把与该goroutine处于同一系统线程的其他goroutine转移到另一个线程中去，以便这些goroutine不阻塞。具体见[go语言] golang的goroutine机制和runtime.GOMAXPROCS

