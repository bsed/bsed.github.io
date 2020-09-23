---
title: Swift - 区间运算符（... 和 ..&lt;）
date: 2015-10-21 22:43:00
updated: 2017-02-17 22:59:45
tags: 
- swift
categories: 
- ios

---
区间运算符可以用来表示两个操作数之间的范围集合。

## 1，闭区间运算符：`a...b`
```swift
for iCount in 512...1024{
     //从512遍历到1024（包括1024）
}
```
## 2，半闭区间运算符：`a..<b`
```swift
var fruts = ["apple","orange","banana"]
let iCount = fruts.count
for i in 0..<iCount{
    print("第\(i+1)个水果是\(fruts[i])")
}
```
## 3，倒序循环
通过 `reversed()` 方法，我们可以将一个正序循环变成逆序循环。
```
for i in (0..<10).reversed() {
    print(i)
}
```
原文:Swift - 区间运算符（`...` 和 `..<`）

## 4，字符串范围也可以使用区间运算符
```swift
//字符串截取
let words = "Mbioq.com"
 
//不使用区间运算符
let index = words.index(words.startIndex, offsetBy: 4)
let index2 = words.index(words.startIndex, offsetBy: 6)
let range1 = Range(uncheckedBounds: (lower: index, upper: index2))
let rangeStr1 = words.substring(with: range1) //ge
print(rangeStr1)
 
//使用区间运算符
let range2 = words.index(words.startIndex, offsetBy: 4)..<words.index(words.startIndex,
                                                                      offsetBy: 6)
let rangeStr2 = words.substring(with: range2) //ge
print(rangeStr2)
```
## 5，区间运算符除了返回一个Range外，还可以接受Comparable的输入，返回`ClosedInterval`或`HalfOpenInterval`
所以我们可以用这个特性检查字符串里的字符是不是都是合法字符（比如是不是都是小写）
```swift
let words = "Mbioq.com"
        
let interval = "a"..."z"
for c in words.characters {
    if !interval.contains(String(c)){
            print("\(c)不是小写字母")
    }
}
```         
//H不是小写字母
//.不是小写字母

原文出自：www.hangge.com  转载请保留原文链接：http://www.hangge.com/blog/cache/detail_513.html