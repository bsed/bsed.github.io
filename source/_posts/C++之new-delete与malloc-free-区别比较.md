---
title: "C++之new delete与malloc free 区别比较"
categories: [ "C/Cpp" ]
tags: [ "c++","new","delete","malloc","free" ]
draft: false
slug: "comparison-between-new-delete-and-malloc-free-in-c"
date: "2020-10-11 18:52:00"
---

## 作用
c语言中提供了malloc 和free 两个系统函数，完成对堆内存的申请和释放。而c++则 提供了两关键字 new 和 delete 。

## 差别

- `malloc/free`是系统函数，而`new/delete`是关键字，系统函数必须包括头文件才能使用而关键字不用。
- `new/delete` 能够兼容`malloc/free`的功能，而且发展出新的功能，即对类对象的处理

## 使用
它们是配套使用的。如果用malloc() 函数申请的空间一定要用 free() 函数释放内存。new() delete() 也一样。

malloc()函数原型：
```c++
void* malloc( std::size_t size )
```
size:以字节为单位的空间
返回一个 void* 类型

例子：
```c++
int a=(int)malloc(sizeof(int));
free(a);
```


<!--more-->


new delete是C++中的关键字可以申请单变量空间，数组空间

//申请单变量空间
//其实跟malloc差不多，malloc也需要声明一个类型及空间大小
```c++
int  *p1=new int;//声明一个整型变量
int  *p2=new int[4];//声明大小为4个整型变量的数组
int (*p3)[4]=new int [3][4]//二维数组
delete p1;
delete []p2;//加个[]表示是数组
delete[]p3;//只要要加一个[]因为数组的话释放空间会迭代释放
```