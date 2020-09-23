---
title: Swift - 下标脚本方法介绍及实例
date: 2015-10-30 09:29:00
updated: 2017-02-19 09:42:53
tags: 
- golang
- 字符编码
categories: 
- go

---
定义下标脚本之后，可以使用`[]`来存取数据类型的值。

示例1：实现一个我们自定的字符串类，可以方便的通过索引获取某一个字符值，或某一部分字符串。同时也可以通过索引，给某一部分赋值。


<!--more-->


```swift
class SubString
{
    var str:String = ""
    init(str:String)
    {
        self.str = str;
    }
     
    /**下标脚本：获取/设置部分字符串**/
    subscript(start:Int, length:Int) -> String
        {
        get{
            let index1 = str.index(str.startIndex, offsetBy: start)
            let index2 = str.index(index1, offsetBy: length)
            let range = Range(uncheckedBounds: (lower: index1, upper: index2))
            return str.substring(with: range)
        }
        set{
            let tmp = str
            str = ""
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
            str = s + newValue + e
        }
    }
     
    /**下标脚本：获取/设置字符**/
    subscript(index:Int) -> String
        {
        get{
            return String(str[str.index(str.startIndex, offsetBy: index)])
        }
        set{
            let tmp = str
            str = ""
            for (idx, item) in tmp.characters.enumerated() {
                if idx == index {
                    str += "\(newValue)"
                }else{
                    str += "\(item)"
                }
            }
        }
    }
}
 
let str = SubString(str:"helloo.com")
print(str[7,3])  //获取字符串：com
print(str[7])  //获取字符：c
 
str[7,3] = "COM"  //设置部分字符串
str[0] = "H" //设置部分字符
print(str[0,10]) //Helloo.COM
```
示例1改进：通过类扩展，也可以直接给String类添加索引功能，代码如下：
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

示例2：使用一维数组结合下标方法一定程度上模拟实现了二维数组
```swift
class Matrix {
    let rows: Int, columns: Int
    var grid: [Double]
     
    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array(repeating: 0.0, count: rows * columns)
    }
     
    func indexIsValidForRow(row: Int, column: Int) -> Bool {
        return row >= 0 && row < rows && column >= 0 && column < columns
    }
     
    subscript(row: Int, column: Int) -> Double {
        get {
            assert(indexIsValidForRow(row: row, column: column), "Index out of range")
            return grid[(row * columns) + column]
        }
        set {
            assert(indexIsValidForRow(row: row, column: column), "Index out of range")
            grid[(row * columns) + column] = newValue
        }
    }
}
 
let value = Matrix(rows: 20,columns: 20)
value[10,10] = 20
print(value[10,10])
```