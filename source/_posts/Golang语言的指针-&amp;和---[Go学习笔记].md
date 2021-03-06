---
title: "Golang语言的指针 &amp;和 * [Go学习笔记]"
categories: [ "Golang" ]
tags: [ "golang" ]
draft: false
slug: "golang-language-pointer-and-go-learning-notes"
date: "2013-12-09 11:32:00"
---

> Go语言保留着C中值和指针的区别，但是对于指针繁琐用法进行了大量的简化，引入引用的概念。所以在Go语言中，你几乎不用担心会因为直接操作内寸而引起各式各样的错误。Go语言的指针，基本上只剩下用于区分
> byref 和 byval 语义。

运算符就是简单的 `&` 和` *` 一个取地址、一个解析地址。

    package main
    import(
        "fmt"
    )
    func main(){
        var i int // i 的类型是int型
        i=1 // i 的值为 1;
        var p *int // p 的类型是[int型的指针]
        p=&i         // p 的值为 [i的地址]
        fmt.Printf("i=%d;p=%d;*p=%d\n",i,p,*p)
    
        *p=2 // *p 的值为 [[i的地址]的指针] (其实就是i嘛),这行代码也就等价于 i = 2
        fmt.Printf("i=%d;p=%d;*p=%d\n",i,p,*p)
        i=3 // 验证想法
        fmt.Printf("i=%d;p=%d;*p=%d\n",i,p,*p)
    }


<!--more-->


**这段代码执行结果：**

    i=1;p=0x4212f100;*p=1
     i=2;p=0x4212f100;*p=2
     i=3;p=0x4212f100;*p=3

函数的参数传递可以看下面例子：

    package main
    import "fmt"
    type abc struct{
        v int
    } 
    func (a abc)aaaa(){ //传入的是值，而不是引用
        a.v=1
        fmt.Printf("1:%d\n",a.v)
    }
    func (a *abc)bbbb(){ //传入的是引用，而不是值
        fmt.Printf("2:%d\n",a.v)
        a.v=2
        fmt.Printf("3:%d\n",a.v)
    }
    func (a *abc)cccc(){ //传入的是引用，而不是值
        fmt.Printf("4:%d\n",a.v)
    }
    func main(){
        aobj:=abc{}  //new(abc);
        aobj.aaaa()
        aobj.bbbb()
        aobj.cccc()
    }

*输出结果：*

    1:1     
    2:0
     3:2
    4:2

## 传值与传指针
当我们传一个参数值到被调用函数里面时，实际上是传了这个值的一份copy，当在被调用函数中修改参数值的时候，调用函数中相应实参不会发生任何变化，因为数值变化只作用在copy上。

传指针比较轻量级 (8bytes),只是传内存地址，我们可以用指针传递体积大的结构体。如果用参数值传递的话, 在每次copy上面就会花费相对较多的系统开销（内存和时间）。所以当你要传递大的结构体的时候，用指针是一个明智的选择。

Go语言中string，slice，map这三种类型的实现机制类似指针，所以可以直接传递，而不用取地址后传递指针。（注：若函数需改变slice的长度，则仍需要取地址传递指针）

要访问指针 p 指向的结构体中某个元素 x，不需要显式地使用 * 运算，可以直接 p.x ；

*一个稍微复杂的例子* 

    package main
    import "fmt"
    type S map[string][]string
    func Summary(paramstring)(s*S){
        s=&S{
            "name":[]string{param},
            "profession":[]string{"Javaprogrammer","ProjectManager"},
            "interest(lang)":[]string{"Clojure","Python","Go"},
            "focus(project)":[]string{"UE","AgileMethodology","SoftwareEngineering"},
            "hobby(life)":[]string{"Basketball","Movies","Travel"},
        }
        return s
    }
    func main(){
        s:=Summary("Harry")
        fmt.Printf("Summary(address):%v\r\n",s)
        fmt.Printf("Summary(content):%v\r\n",*s)
    
    }

*输出*：

    Summary(address): 0x42131100     
    Summary(content): map[profession:[Java programmer Project Manager] interest(lang):[Clojure Python Go] hobby(life):[Basketball Movies Travel] name:[Harry] focus(project):[UE Agile Methodology Software Engineering]]
         exit code 0, process exited normally.     


参考资料：

使用Go语言一段时间的感受
http://blog.jobbole.com/14386/

[Go 语言] 指针,地址,形参,实参
http://my.oschina.net/nalan/blog/77373

从例子中学习 go 语言 —— 数据结构、指针
http://ilovers.sinaapp.com/drupal/node/33
