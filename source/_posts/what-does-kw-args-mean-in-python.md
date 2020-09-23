---
title: python中*args **kw到底是什么意思
date: 2015-08-04 17:58:00
updated: 2015-08-04 18:04:57
tags: 
- css
- 加密
categories: 
- css

---
# *args是非关键字参数，用于元组，**kw是关键字参数，用于字典

> Python: 函数
> author：lxy
> 原文链接：[http://www.2cto.com/kf/201409/331170.html](http://www.2cto.com/kf/201409/331170.html)

函数就是一些具有特定功能抽象出来供给多次访问的语句，和java中方法一样。
通过抽象，我们可以不用关心函数底层具体调用过程，而直接在更高的层次上思考问题，这企不是和站在巨人的肩膀上远观一样吗。

## 函数的调用
Python中提供了很多函数我们可以直接调用
在调用函数之前应该明白函数名，和具体参数
当参数传递错误时，会报TypeErro错误


<!--more-->


对于Python内置提供的函数通过Python帮助可以查阅

    abs(number) #返回绝对值 
    cmp(number1,number2) #比较大小，前>后 返回1
    isinstance(x,(int,str)) #参数类型检查

数据类型转换的函数

    int() #将能转为整数的转为整数，不能转换的报错
    float() #转浮点型
    str() #转字符串
    unicode() #编码转换
    bool() #转为布尔型

# 函数的定义
在Python中定义方法使用def语句，然后依次写方法名、括号、括号中写参数、括号后冒号：、然后在缩进块中写方法体，
函数的返回值用return返回，如果没有return语句，函数执行完返回值为None

例：定义一个函数计算两个数的加和

    >>> def sum(x,y):
    return x+y
    
    >>> a = sum(2,3)
    >>> print(a)
    5

## 空函数
空函数就是什么也不做的函数，用pass语句占位
pass也可以放在其他语句中，如果没有pass语句就会报语法上面的错误

## 返回多个值的函数
return 返回值之间用逗号分隔开，接收返回值的多个参数也用逗号相分隔
但其实返回俩个值是一个假象，返回的是一个tuple，如下：

    >>> t = (5,8)
    >>> a,b = t
    >>> a
    5
    >>> b
    8
    >>> 

## 函数的参数
函数的名字和位置确定下来，函数的接口定义就完成了，
对于调用者，只需要知道如何传递正确的参数和函数返回什么样的值就够了
Python函数定义简单，但灵活，参数可以有多种定义方式：必须参数、默认参数、可变参数、关键字参数

## 默认参数
给参数赋一个默认值，当不传参数的时候使用默认设置的值进行运算，简化了函数的调用

**注意：**
1、必选参数放在前面，默认参数放在后面。
2、多个默认参数时，变化小的放在后面
3、定义默认参数时，默认参数必须指向不变的对象

设计str和None这样不变对象的好处
1、不变对象创建就不可变，减少了由于修改数据导致的错误
2、多任务执行不必加锁（能用不变对象尽量使用不变对象）

## 可变参数
当传递参数个数不确定的时候我们可以考虑传递list或tuple
但是使用可变参数更为简化
可变参数的定义，只需要在参数前面加一个*，然后通过循环遍历传递给函数的参数即可
例；定义一个可变参数求和函数

    >>> def calc(*ns):
    sum = 0
    for n in ns:
    sum = sum + n;
    return sum
    
    >>> calc(1)
    1
    >>> calc(1,5)
    6
    >>> 

对于list或tuple传递到可变参数中，在传递时在参数前加上*即可

## 关键字参数
可以传入0个活任意多个含参数名的参数，这些关键字参数在函数中自动组装成一个dict
定义时在参数前使用**

## 参数组合
在Python中定义函数时参数可以是一种或多种参数一起使用
注意参数定义顺序必须是：必须参数、默认参数、可变参数、关键字参数
递归函数
函数内部调用自身的函数

例：数学运算之阶乘

    >>> def fact(n):
    if n == 1:
    return 1
    return n * fact(n - 1)
    
    >>> a = fact(3)
    >>> print(a)
    6
    >>>

递归函数的定义简单，逻辑清晰，理论上所有的递归函数都可以写成循环的方式。

注意，使用递归函数需要注意栈溢出，在计算机中，函数调用是通过栈这种数据结构实现的，每进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减去一层栈帧，当递归调用的次数过多，会导致栈溢出。

解决递归调用栈溢出：尾递归
尾递归是指，在函数返回的时候，调用自身，return语句不能包含表达式，这样，不管尾递归调用多少次在，都只占用一个栈帧
尾递归和循环函数的效果是一样的，所以把循环看成是一种特殊的尾递归函数。
Python标准的解释器没有针对尾递归做优化，任何递归都存在栈溢出问题

**总结：**
Python的函数具有非常灵活的参数形态，既可以实现简单的调用，又可以传入非常复杂的参数。
默认参数一定要用不可变对象，如果是可变对象，运行会有逻辑错误！
要注意定义可变参数和关键字参数的语法：
`*args是可变参数，args接收的是一个tuple；`
`**kw是关键字参数，kw接收的是一个dict`。
以及调用函数时如何传入可变参数和关键字参数的语法：
可变参数既可以直接传入：func(1, 2, 3)，又可以先组装list或tuple，再通过*args传入：func(*(1, 2, 3))；
关键字参数既可以直接传入：func(a=1, b=2)，又可以先组装dict，再通过**kw传入：func(**{'a': 1, 'b': 2})。
使用*args和**kw是Python的习惯写法，当然也可以用其他参数名，但最好使用习惯用法

一个小例子：
### *args
 

    def tupleArgs(arg1, arg2= 'B', *arg3):
        print('arg 1:%s ' % arg1)
        print('arg 2:%s ' % arg2)
        for eachArgNum in range(len(arg3)):
            print('the %d in arg 3 :%s ' % (eachArgNum,arg3[eachArgNum]))
    if __name__ == '__main__':
        tupleArgs('A')      
        #   arg 1:A 
        #   arg 2:B 
        tupleArgs('23','C')
        #   arg 1:23 
        #   arg 2:C
        tupleArgs('12','A','GF','L')
        #   arg 1:12 
        #   arg 2:A 
        #   the 0 in arg 3 :GF 
        #   the 1 in arg 3 :L 
     
### **kw
     
   

     def dictArgs(kw1, kw2= 'B', **kw3):
        print('kw 1:%s ' % kw1)
        print('kw 2:%s ' % kw2)
        for eachKw in kw3:
            print('the %s ---->:%s ' % (eachKw,kw3[eachKw]))
    if __name__ == '__main__':
        dictArgs('A')
        #   kw 1:A 
        #   kw 2:B 
        dictArgs('23','C')
        #   kw 1:23 
        #   kw 2:C 
        dictArgs('12','A', c = 'C',d = '12121212')
        #   kw 1:12 
        #   kw 2:A 
        #   the d ---->:12121212 
        #   the c ---->:C 
        dictArgs('kw',c = 'C',d = '12121212',kw = 'KW')
        #   kw 1:kw 
        #   kw 2:B 
        #   the kw ---->:KW 
        #   the d ---->:12121212 
        #   the c ---->:C