---
title: Swift - 使用arc4random()、arc4random_uniform()取得随机数
date: 2015-11-18 23:20:00
updated: 2017-02-17 23:21:31
tags: 
- swift
categories: 
- ios

---
`arc4random()` 这个全局函数会生成10位数的随机整数（`UInt32`）。其生成的最大值是`4294967295（2^32 - 1）`，最小值为`0`。

## 1，下面是使用 `arc4random` 函数求一个 `1~100` 的随机数（包括1和100）
```swift
let temp = Int(arc4random()%100)+1
```
## 2，下面是使用 arc4random_uniform 函数求一个 1~100 的随机数（包括1和100）
```swift
let temp = Int(arc4random_uniform(100))+1
```
