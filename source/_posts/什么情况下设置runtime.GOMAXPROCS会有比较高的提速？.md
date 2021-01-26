---
title: "什么情况下设置runtime.GOMAXPROCS会有比较高的提速？"
categories: [ "Golang" ]
tags: [ "golang" ]
draft: false
slug: "under-what-circumstances-runtimegomaxprocs-will-have-a-relatively-high-speed"
date: "2014-11-09 17:58:00"
---

> 我们都知道Go默认是用一个CPU核心的，除非手动设置runtime.GOMAXPROCS。网上说有时设置了使用多核速度反而会下降。请问什么情况下设置这个函数会有比较好的加速？爬虫之类的应用可以吗？

我的看法： 适合于CPU密集型、并行度比较高的情景。 爬虫如果只是抓网页而不分析，只是并发度比较高，属于I/O密集型，并不需要开太大的GOMAXPROCS。

GOMAXPROCS最好不要超过NumCPU，不然CPU之间切换也是浪费时间


<!--more-->


这是我测试的代码

    // parallel package main
    
    import ( "fmt" "math/rand" "runtime" "sort" "time" )
    
    func testData() [][]int { now := time.Now() src := rand.NewSource(now.UnixNano()) seed := rand.New(src) data := make([][]int, 10000) for i := 0; i < len(data); i++ { data[i] = make([]int, 10000) for j := 0; j < 10000; j++ { data[i][j] = seed.Intn(100000) } } return data }
    
    func test() { data := testData() ch := make(chan int) for i := 0; i < len(data); i++ { go func(ch chan int, data []int) { sort.Ints(data[:]) ch <- 1 }(ch, data[i][:]) } for i := 0; i < len(data); i++ { <-ch } }
    
    func main() { st := time.Now() test() fmt.Println(time.Since(st)) runtime.GOMAXPROCS(2) st = time.Now() test() fmt.Println(time.Since(st)) runtime.GOMAXPROCS(3) st = time.Now() test() fmt.Println(time.Since(st)) runtime.GOMAXPROCS(4) st = time.Now() test() fmt.Println(time.Since(st)) fmt.Println("Hello World!") }

该代码的作用是生成10000个数组，每个数组有10000个int元素，分别调用不同CPU核数进行排序计算。用的是Go内置的排序函数。

中的时间如下

    25.6269405s
    14.1753705s
    10.3508423s
    8.5466479s

分别是单核，2核，3核，4核的计算时间。的确用多核后计算速度提升很大。


不是这样的，GOMAXPROCS的最大上限是256。 相关源码在proc.c文件的runtime·gomaxprocsfunc函数和procresize函数（go1.3.1）。

link :[http://www.golangtc.com/t/53f1a957320b520f4b000050](http://www.golangtc.com/t/53f1a957320b520f4b000050)