---
title: PHP中this和self对虚函数的处理
date: 2015-04-01 10:05:00
updated: 2016-05-29 10:07:45
tags: 
- golang
categories: 
- java

---
## 简介

最近发现一处this和self对虚函数的不同处理方式，网上查阅文章之后明白了两者的区别，特此记录。
例子：
```php
class A{
    public function callFuncXXOO(){
        print $this->funcXXOO();
    }
    public function funcXXOO(){
        return "A::funcXXOO";
    }
}
class B extends A {
    public function funcXXOO(){
        return "B::funcXXOO";
    }
}
$b = new B;
$b->callFuncXXOO();
```


<!--more-->


结果：

```
B::funcXXOO
```
可以看到，当在 A 中使用 `$this->funcXXOO()` 时，体现了“虚函数”的机制，实际调用的是 `B::funcXXOO()`. 
然而如果将所有函数都改为静态函数：
```php
class A{
    static public function callFuncXXOO(){
        print self::funcXXOO;
    }
    static public function funcXXOO() {
        return "A::funcXXOO()";
    }
}
class B extends A{
    static public function funcXXOO(){
         return "B::funcXXOO";
    }
}
$b = new B;
$b->callFuncXXOO();
```
结果：
```
A::funcXXOO
```

## 结论

这是因为 `self` 的语义本来就是“当前类”，
所以 `PHP5.3` 给 static 关键字赋予了一个新功能：后期静态绑定：
将上面代码中的`self`改为`static`，则会和预期一样输出 `B::funcXXOO` 了。