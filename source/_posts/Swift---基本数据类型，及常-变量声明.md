---
title: "Swift - 基本数据类型，及常/变量声明"
categories: [ "IOS" ]
tags: [ "swift" ]
draft: false
slug: "swift-basic-data-types-and-constant-variable-declarations"
date: "2015-08-26 22:35:00"
---

下面是Swift中基本的数据类型介绍说明：

## 1，变量：使用var声明
```swift
var str:String = "hangge.com"
```
## 2，常量：使用let声明
```swift
let count:Int = 32
```
## 3，整形：


<!--more-->


有符号整形：`Int`，`Int8`，`Int16`，`Int32`，`Int64`
无符号整形：`UInt`，`UInt8`，`UInt16`，`UInt32`，`UInt64`

## 4，浮点型：
`Float`：32位浮点数类型
`Double`：64位浮点数类型

## 5，布尔型：`Bool` 

## 6，字符串：String 
（1）判断是否为空：isEmpty
```swift
var str:String = ""
if str.isEmpty{
      print("空字符串")
}
```
（2）获取字符数量：`string.characters.count`
```swift
let str = "mbioq.com"
print("\(str.characters.count)个字符")
```
（3）检查字符串是否有特定前缀/后缀：`hasPrefix/hasSuffix`
```swift
var str = "mbioq.com"
if str.hasSuffix(".com"){
}
```
（4）还可以用“\()”在字符串里包裹变量，常量
```
let name = "mbioq.com"
let msg = "欢迎来到 \(name)"
```
（5）大小写转换：
通过字符串的`uppercaseString`、`lowercaseString`、`capitalizedString`属性来访问一个字符串的大写/小写/首字母大写
```swift
let str = "Welecome to mbioq.com"
         
var uppercase = str.uppercaseString  //WELECOME TO MBIOQ.COM
var lowercase = str.lowercaseString  //welecome to mbioq.com
var capitalized = str.capitalizedString  //Welecome To Mbioq.Com
```
## 7，字符：`Character`
```
var doller:Character = "$"
```
## 8，元组
```
let myProject = (one:"game",two:1234)
print("\(myProject.one)")
```