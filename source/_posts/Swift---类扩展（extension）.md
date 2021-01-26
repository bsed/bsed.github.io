---
title: "Swift - 类扩展（extension）"
categories: [ "IOS" ]
tags: [ "swift" ]
draft: false
slug: "swift-class-extension-extension"
date: "2015-11-02 21:29:00"
---

Swift语言的类扩展是一个强大的工具，我们可以通过类扩展完成如下事情：
1，给已有的类添加计算属性和计算静态属性
2，定义新的实例方法和类方法
3，提供新的构造器
4，定义下标脚本
5，是一个已有的类型符合某个协议
（**注意**：扩展只能添加新的计算型属性，不能添加存储型属性，也不能添加新的属性监视器）

示例1：给字符串String类添加下标脚本，支持索引访问


<!--more-->


```swift
extension String
{
    subscript(start:Int, length:Int) -> String
        {
        get{
            let index1 = self.index(self.startIndex, offsetBy: start)
            let index2 = self.index(index1, offsetBy: length)
            let range = Range(uncheckedBounds: (lower: index1, upper: index2))
            return self.substring(with: range)
        }
        set{
            let tmp = self
            var s = ""
            var e = ""
            for (idx, item) in tmp.characters.enumerated() {
                if(idx < start)
                {
                    s += "\(item)"
                }
                if(idx >= start + length)
                {
                    e += "\(item)"
                }
            }
            self = s + newValue + e
        }
    }
    subscript(index:Int) -> String
        {
        get{
            return String(self[self.index(self.startIndex, offsetBy: index)])
        }
        set{
            let tmp = self
            self = ""
            for (idx, item) in tmp.characters.enumerated() {
                if idx == index {
                    self += "\(newValue)"
                }else{
                    self += "\(item)"
                }
            }
        }
    }
}
 
var str = "helloo.com"
print(str[7,3])
print(str[7])
 
str[7,3] = "COM"
str[0] = "H"
 
print(str[0,10])
```


示例2：给Double增加mm，cm等方法，进行进制转换
```swift
extension Double
{
    func mm()->String
    {
        return "\(self/1)mm"
    }
    func cm()-> String
    {
       return "\(self/10)cm"
    }
     
    func dm()->String{
        return "\(self/100)dm"
    }
     
    func m()->String
    {
        return "\(self/1000)m"
    }
    func km()->String
    {
        return "\(self/(1000*1000))km"
    }
}
 
let value = 2000000000.0
print(value.mm())
print(value.cm())
print(value.dm())
print(value.m())
print(value.km())
```
运行结果如下：