---
title: Swift - 复杂数据类型说明（数组，字典，结构体，枚举）
date: 2015-10-20 13:29:00
updated: 2017-02-18 13:35:25
tags: 
- swift
categories: 
- java

---
## 1，数组 - Array
```swift
var types = ["none","warning","error"]  //省略类型的数组声明
         
var members = [String]() //声明一个空数组
         
members.append("six")  //添加元素
members += ["seven"] //添加元素
members.insert("one", atIndex:0)  //指定位置添加元素
         
members[0] = "message"  //通过下标修改数组中的数据
members[0...2] = ["message","mbioq","com"]  //通过小标区间替换数据（前3个数据）
         
members.count  //获取数组元素个数
members.isEmpty  //判断数组是否为空
         
members.removeAtIndex(2)  //删除下标为2的数组
members.removeLast()  //删除最后一个元素
members.removeAll(keepCapacity:true)  //删除数组中所有元素
         
let addStringArr = types + members //数组组合
         
//使用for in 实现数组遍历
for value in members{
    print("\(value)");
}
         
//通过enumerate函数同时遍历数组的所有索引与数据值
for (index,value) in members.enumerate(){
    print("索引：\(index) 数据：\(value)");
}
 
//交换元素位置（第2个和第3个元素位置进行交换）
swap(&menbers[1], &menbers[2])
```


<!--more-->


## 2，字典 - Dictionary（即键值对）
```swift
var empty = [String: Int]()  //建立个空字典
 
var myDic = ["name":"mbioq",
    "url":"mbioq.com"]  //声明一个字典
 
myDic["address"] = "china" //添加或修改key值
myDic.removeValueForKey("name")  //删除"name"这个key值
myDic["name"] = nil  //同样可以删除"name"这个key值
myDic.keys  //访问字典的key集合
myDic.values //访问字典的value集合
 
//遍历字典
for (key,value) in myDic {
    print("\(key):\(value)");
}
 
//只遍历字典的键（key）
for key in myDic.keys {
    print("\(key)");
}
 
//只遍历字典的值（value）
for value in myDic.values {
    print("\(value)");
}
```
## 3，结构体 - struct
```swift
//创建一个结构体
struct BookInfo{
    var ID:Int = 0
    var Name:String = "Defaut"
    var Author:String = "Defaut"
}
 
var book1:BookInfo //默认构造器创建结构体实例
var book2 = BookInfo(ID:0021,Name:"kelvin",Author:"mbioq")  //调用逐一构造器创建实例
book2.ID = 1234  //修改内部值
```
## 4，枚举 - enum
```swift
enum CompassPoint {
    case North
    case South
    case East
    case West
}
var directionToHead = CompassPoint.West
 
enum Planet: Int {
    case Mercury = 1
    case Venus = 2
    case Earth = 3
}
let earthsOrder = Planet.Earth.rawValue //rawValue来获取他的原始值:3
let possiblePlanet = Planet(rawValue: 2)  //通过原始值来寻找所对应的枚举成员:Venus
 
enum Direction {
    case Up
    case Down
     
    func description() -> String{
        switch(self){
        case .Up:
            return "向上"
        case .Down:
            return "向下"
        }
    }
}
print(Direction.Up.description())
```