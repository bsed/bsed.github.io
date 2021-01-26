---
title: "Swift - 类初始化和析构方法（init与deinit）"
categories: [ "IOS" ]
tags: [ "swift" ]
draft: false
slug: "swift-class-initialization-and-configuration-methods-init-and-deinit"
date: "2015-10-25 08:34:00"
---

## 1，init()：类的初始化方法（构造方法）
```swift
/********************************
使用默认构造函数
********************************/
class Student{
    //类属性
    var name:String = ""
    var number:Int = 0
}
var student = Student()
 
/********************************
自定义构造函数
********************************/
class Person{
    //类属性
    var name:String
    var age:Int
 
    //类构造函数
    init(newName:String, newAge:Int){
        self.name = newName
        self.age = newAge
    }
 
    //类方法
    func say() -> String{
        return "我叫\(name)"
    }
}
var p = Person(newName: "mbioq",newAge: 32)
print(p.say())
```


<!--more-->


## 2，deinit()：类反初始化方法（析构方法）
```swift
class DBClass{
    var conn:Connection? = Connection()
    deinit{
        //可以做一些清理工作
        self.conn!.close()
        self.conn = nil
    }
}
 
var db:DBClass? = DBClass()
db = nil //设置nil后即可执行deinit()方法
```