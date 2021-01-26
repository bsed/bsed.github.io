---
title: "Swift - 运算符重载和运算符函数"
categories: [ "IOS" ]
tags: [ "swift" ]
draft: false
slug: "swift-operator-overloading-and-operator-functions"
date: "2015-09-29 13:25:00"
---

让已有的运算符对自定义的类和结构进行运算或者重新定义已有运算符的运算规则，这种机制被称为`运算符重载`。

## 1，通过重载加号运算符，使自定义的两个坐标结构体对象实现相加：
```swift
struct CenterPointer{
    var x=0, y=0
}
 
func + (left:CenterPointer, right:CenterPointer) -> CenterPointer{
    return CenterPointer(x:left.x+right.x, y:left.y+right.y)
}
 
let pointer1 = CenterPointer(x:2, y:3)
let pointer2 = CenterPointer(x:4, y:5)
let pointer3 = pointer1 + pointer2
```


<!--more-->


## 2，重载判断运算符，实现判断自定义类型是否相等
```swift
func == (left:CenterPointer, right:CenterPointer) -> Bool {
    return (left.x == right.x) && (left.y == right.y)
}
 
func != (left:CenterPointer, right:CenterPointer) -> Bool {
    return !(left == right)
}
```
## 3，组合运算符，即将其他运算符和赋值运算符组合在一起，注意要把运算符左参数设置成`inout`类型
```swift
func += (left:inout CenterPointer, right:CenterPointer){
    left = left + right
}
 
var pointer1 = CenterPointer(x:2, y:3)
var pointer2 = CenterPointer(x:4, y:5)
pointer1 += pointer2
```
