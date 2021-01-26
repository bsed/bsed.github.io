---
title: "包、指针和内存分配[Golang学习]"
categories: [ "Golang" ]
tags: [ "golang","指针" ]
draft: false
slug: "packet-pointer-and-memory-allocation-golang-learning"
date: "2013-06-24 12:19:00"
---

## package

包名约定使用*小写字符*。包中的函数名以*大写字母*开头的是可导出函数，小写字母是*私有函数*。这个规定同样适用于新类型、全局变量。
当包导入（通过import）时，包名成为了内容的入口。在`import "bytes"`之后，导入包的可以调用函数`bytes.Buffer`。任何使用这个包的人，可以使用同样的名字访问到它的内容，因此这样的包名是好的：短的、简洁的、好记的。

根据规则，包名是小写的一个单词；**不应当有下划线或混合大小写**。由于每个人都可能需要录入这个名字，所以尽可能的简短。不要提前考虑冲突。
包名是导入的默认名称。

在Go中使用混合大小写`MixedCaps`或者`mixedCaps`，而不是下划线区分含有多个单词的名字。

## 包文档：

每个包都应该有包注释，在package 前的一个注释块。对于多文件包，包注释只需要出现在一个文件前，任意一个文件都可以。
包注释应当对包进行介绍，并提供相关于包的整体信息。这会出现在`go doc` 生成的关于包的页面上，并且相关的细节会一并显示。
每个定义（并且导出）的函数应当有一小段文字描述该函数的行为。

常用包：[http://golang.org/pkg/](http://golang.org/pkg/)

## 指针
go有指针，没有指针运算。一个新定义的没有指向的指针值为 `nil`。
```golang
package main
import "fmt"
 
func main(){
var p *int
fmt.Printf("%v\n",p)
fmt.Printf("%T\n",p)
var i int
p = &i
fmt.Printf("%v\n",p)
*p =8
fmt.Printf("%v\n",*p)
}
```
结果：

*int
0x10dd0018
8

*p++,表示(*p)++,先获取指针指向的值，然后 `+1`。

## 内存分配

### 使用new分配内存

`new(T)`分配了零值填充的T类型的内存空间，返回其地址，一个*T类型的值。返回一个指针，指向新分配的类型T的零值。

### 用make分配内存

make(T,args)只能创建`slice`，`map`和`channel`，并且返回一个有初始值（非零）的T类型，不是`*T`.原因是**指向数据结构的引用在使用前必须被初始化**。
v := make([]int,100)

定义自己的类型

```golang
type person struct {
    name string
    age int
}
var P person
P.name = "Tom" // Assign "Tom" to P's name field.
P.age = 25 // Set P's age to 25 (an int).
fmt.Println("The person's name is %s", P.name)
```
P是一个person类型的变量。

```golang
package main
import "fmt"
 
type person struct{
    name string
    age int
}
 
func main(){
    a := new(person)
    a.name = "wang"
    a.age = 42
    b := person{"Tom",11}
    c := person{"rose",44}
    fmt.Printf("%v,%v,%v",a,b,c)
}
```
字段名字大写，可以导出，可以在其他包使用。小写则不可以。

[http://0x55aa.sinaapp.com/%E7%AE%97%E6%B3%95-%E7%BC%96%E7%A8%8B/650.html](http://0x55aa.sinaapp.com/%E7%AE%97%E6%B3%95-%E7%BC%96%E7%A8%8B/650.html)