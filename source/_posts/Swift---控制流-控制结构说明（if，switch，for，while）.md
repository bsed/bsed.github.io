---
title: "Swift - 控制流/控制结构说明（if，switch，for，while）"
categories: [ "IOS" ]
tags: [ "swift" ]
draft: false
slug: "swift-control-flow-control-architecture-specification-if-switch-for-while"
date: "2015-10-22 13:53:00"
---

## 1，if语句
```swift
if count >= 3 {
    print("yes")
}else{
    print("no")
}
```
## 2，switch语句 
（1）Swift中不需要在case块中显示地使用break跳出switch。如果想要实现C风格的落入特性，可以给需要的case分支插入fallthrough语句
```swift
let fruit = "apple"
switch fruit{
    case "apple":
        print("good")
        fallthrough
    case "banana","orange":
        print("great")
    default:
        print("bad")
}
```


<!--more-->


（2）case分支还可以进行区间匹配
```swift
let age = 5
switch age {
    case 0...11:
        print("正太")
    case 12...30:
        print("少年")
    default:
        print("大叔")
}
```
（3）使用元组匹配（判断属于哪个象限）
```
let point = (2,2)
switch point {
    case (0,0):
        print("坐标在原点")
    case (_,0):
        print("坐标在x轴上")
    case (0,_):
        print("坐标在y轴上")
    case (-3...3, -3...3):
        print("坐标在长宽为6的正方形内")
    default:
        print("在什么地方")
}
（4）case中还可以使用where关键字来做额外的判断条件
```swift
var height = 1.72
switch height{
    case 1...3 where height == 1.72:
        print("case 1")
    case 1...3 where height == 2:
        print("case 2")
    default:
        print("default")
}
```
## 3，for循环语句 
（1）for条件递增循环（注意：这种C语言风格的for循环语法已在Swift3中被废除，建议使用下面的for-in，for-each写法）
```swift
//已弃用
//for var i=1; i<100; i++ {
//  print("\(i)")
//}
```
（2）for-in循环
```swift
for i in 1..<100{
    print("\(i)")
}
//遍历数组元素
let numbers = [1,2,4,7]
for num in numbers{
    print("\(num)")
}

//遍历字典
let nameOfAge = ["lily":18, "Candy":24]
for (aName, iAge) in nameOfAge{
    print("\(aName) is \(iAge)")
}
 
//遍历字符串的字符
for chare in "mbioq".characters {
    print(chare)
}
```
（3）for-each循环
```swift
(1...10).forEach {
    print($0)
}
```
## 4，while循环语句
```swift
while i<100 {
    i+=1
}
 
repeat{
    i+=1
}while i<100
```
