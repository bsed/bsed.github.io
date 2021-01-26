---
title: "Golang自定义Map的实现"
categories: [ "Golang" ]
tags: [ "golang","map" ]
draft: false
slug: "custom-map-implementation"
date: "2015-10-11 18:23:00"
---

现在要实现一个特殊的 map ，支持 push 和 pop 两个操作。看起来是这样的：

    type myMap interface {
        push(key string, e interface {}) interface{} 
        pop(key string) interface{}
    }

当 push 的时候，如果 map 中 key 已存在，返回原来对应的 value ；若 key 不存在，则创建一个新的 key 把 value 放进去。

而 pop 操作，返回 key 对应的 value ，并把 key/value 对从 map 中删除。


<!--more-->


鉴于 Go 程序中通常会使用大量 goroutine ，所以，这个 map 应该是线程安全的。那么用 Go 怎么实现它呢？最简单也就是最传统的方式是使用锁，即 sync.Mutex ，代码如下：


    package main
    
    import (
        "fmt"
        "sync"
    )
    
    type myMap struct {
        m map[string] interface {}
        sync.Mutex
    }
    
    func (m *myMap) push(key string, e interface {}) interface {} {
        m.Lock()
        defer m.Unlock()
        if v,exist := m.m[key] ; exist {
            return v;
        }
        m.m[key] = e
        return nil
    }
    
    func (m *myMap) pop(key string) interface {} {
        m.Lock()
        defer m.Unlock()
        if v,exist := m.m[key] ; exist {
            m.m[key] = nil, false
            return v
        }
        return nil
    }
    
    func newMap() *myMap {
        return &myMap { m: make(map[string] interface {}) }
    }
    
    func main() {
        m := newMap()
        fmt.Println(m.push("hello","world"))
        fmt.Println(m.push("hello","world"))
        fmt.Println(m.pop("hello"))
        fmt.Println(m.pop("hello"))
    }

这里，newMap() 方法会返回一个 myMap 指针。其实按之前的定义，返回 muMap interface 也可以，它们在功能上是等价的。 但这里不可以返回 myMap 结构。因为，其中包含有一个其它包里的结构 sync.Mutex ，它是不可以被复制的。Go 里面没有 C++ 中重载赋值运算那些污七八糟的语法糖，所以用指针就好了。反正有 gc 不用担心。

其实，这个东东也可以把所有对 map 的操作放到同一个 goroutine 里完成，就不需要显式的锁了。不过具体到这个需求上，这么实现的意义实在有限。下面列出代码来，只是说可以这么做而已。

    package main
    
    import "fmt"
    
    type myMap interface {
        push(key string, e interface {}) interface{} 
        pop(key string) interface{}
    }
    
    type myMapPair struct {
        key string
        value interface {}
    }
    
    type mapChan struct {
        push_req chan * myMapPair
        push_rep chan interface{}
        pop_req chan string
        pop_rep chan interface{}
    }
    
    func (c *mapChan) push(key string, e interface{}) interface{} {
        c.push_req <- & myMapPair {key,e}
        return <- c.push_rep
    }
    
    func (c *mapChan) pop(key string) interface {} {
        c.pop_req <- key
        return <- c.pop_rep
    }
    
    func newMap() myMap {
        c := mapChan { 
            push_req : make (chan * myMapPair),
            push_rep : make (chan interface{}),
            pop_req : make (chan string),
            pop_rep : make (chan interface{}),
        }
        m := make(map[string] interface {})
        go func() {
            for {
                select {
                case r := <- c.push_req :
                    if v , exist := m[r.key] ; exist {
                        c.push_rep <- v
                    } else {
                        m[r.key] = r.value
                        c.push_rep <- nil
                    }
                case r := <- c.pop_req:
                    if v,exist := m[r] ; exist {
                        m[r] = nil, false
                        c.pop_rep <- v
                    } else {
                        c.pop_rep <- nil
                    }
                }
            }
        } ()
        return &c   
    }
    
    func main() {
        m := newMap()
        fmt.Println(m.push("hello","world"))
        fmt.Println(m.push("hello","world"))
        fmt.Println(m.pop("hello"))
        fmt.Println(m.pop("hello"))
    }

这里建立了四个 chan ，也就是两对请求/回应通道，让单一的 goroutine 来处理两种对 map 操作的请求。这个处理的 goroutine 可以看成是对其它 goroutine 提供的服务。能这么方便的使用这种模式，在大多数编程语言里并不多见。Erlang 可以算一个。但 Go 的优势在于，允许你使用可能对你更为习惯的命令式编程方式，而不需要转换思维到函数式编程。