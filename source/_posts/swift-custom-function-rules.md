---
title: Swift - 自定义函数规则说明
date: 2015-10-24 13:59:00
updated: 2017-02-18 14:04:44
tags: 
- swift
categories: 
- ios

---
## 1，无返回值的函数
```swift
func test(name:String){
 
}
```
## 2，返回一个返回值
```
func test(name:String) -> Bool{
    return true
}
```


<!--more-->


## 3，返回由多个值组成的复合返回值
```swift
func test(name:String) -> (Int,Bool){
    let position = 1
    let visible = false
    return (position,visible)
}
```
## 4，可变形参：可以接受0个或者任意数量的输入参数
```swift
func test(numbers:Int...) -> Int{
    var count:Int = 0
    for number in numbers{
        count += number
    }
    return count
}
```
## 5，如果想要同时改变函数内外的参数值，可以利用inout关键字，同时调用函数的时候给参数加上前缀“&”
```swift
func add(age:inout Int){
    age += 1
}
 
var age = 22
add(age: &age)
print(age) //23
```
## 6，可以使用函数类型的参数
```swift
func additive(a:Int, b:Int) -> Int{
    return a + b
}
 
//函数类型的参数
func printAdditiveResult(addFun: (Int, Int) -> Int, a:Int, b:Int){
    print("Result:\(addFun(a,b))")
}
 
printAdditiveResult(addFun: additive, a: 5 ,b: 7)
```
## 7，也可以使用函数类型的返回值
```swift
//定义个自增函数
func increase(input:Int) -> Int{
    return input + 1
}
 
//定义个自减函数
func reduce(input:Int) -> Int{
    return input - 1
}
 
//定义一个返回函数类型的函数
func chooseFunction(backwards:Bool) -> (Int) -> Int{
    return backwards ? reduce : increase
}
 
//测试
let aFun = chooseFunction(backwards: 3>2)
print(aFun(3))  //2
```