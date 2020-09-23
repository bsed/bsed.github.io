---
title: Swift - 类型属性（类静态属性）和类方法（类静态方法）
date: 2015-10-28 09:12:00
updated: 2017-02-19 09:13:47
tags: 
- swift
categories: 
- ios

---
## 1，结构体struct和枚举enum的静态属性，静态方法使用static关键字
```swift
struct Account { 
    var amount : Double = 0.0                 //账户金额 
    var owner : String = ""                   //账户名 
 
    static var interestRate : Double = 0.668  //利率
 
    static func interestBy(amount : Double) -> Double {
        return interestRate * amount 
    }
}
```


<!--more-->


## 2，类class的类型属性，类型方法使用class关键字
```swift
class Account {
    var amount : Double = 0.0               // 账户金额 
    var owner : String = ""                 // 账户名 
  
    class var staticProp : Double {
        return 0.668 
    } 
 
    class func interestBy(amount : Double) -> Double {
        return 0.8886 * amount 
    }
} 
   
//访问静态属性 
print(Account.staticProp)
```