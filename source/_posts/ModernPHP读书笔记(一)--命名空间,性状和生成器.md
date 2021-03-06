---
title: "ModernPHP读书笔记(一)--命名空间,性状和生成器"
categories: [ "PHP" ]
tags: [ "php","ModernPHP" ]
draft: false
slug: "modernphp-book-notes-1-namespace-characters-and-generators"
date: "2015-12-25 10:20:00"
---

## 简介

 1. `ModernPHP` 这是一本以现代眼光重新学习PHP的书籍,介绍了截至到`PHP 5.6`甚至`PHP 7`的特性和使用.
 2. 读书笔记会选择我在学习时遇到的难点和不理解的地方,通过代码和查询资料加深记忆.
 3. 然后整理成笔记记录,以便以后查看.

##命名空间

### 版本要求
```
5.3.0及以上
```
### 使用命名空间


<!--more-->


```php
使用namespace声明所属命名空间,声明需要在<?php之后的第一行进行,子命名空间之间使用\符号分隔,如:
namespace CodeApe\TestClass;
代表接下来的代码处于CodeApe命名空间的TestClass子命名空间内.
使用use关键字引入命名空间,并且可以为引入的命名空间创建别名,无别名时使用类名作为名称,引入的位置在<?php和namespace(如有)之后如:
<?php
    use CodeApe\TestClass\testClass;
此时下面的类中可以使用testClass进行调用
<?php
    use CodeApe\TestClass\testClass as tc;
此时下面的类中可以使用tc进行调用

```
## 注意事项

1.命名空间是为了解决项目开发时的类名或其他冲突,规范PHP项目结构.
2.命名空间与文件实际存放文件夹并无强制联系,但是良好的相互联系是类自动加载的基础,也是PSR-4所倡导的.
3.PHP 5.3.0开始支持命名空间,类和接口的引入及创建别名,5.6开始支持引入函数和常量及为其创建别名.

## 性状

### 版本要求
```
5.4.0及以上
```
### 性状使用
```
使用trait定义一个性状,与定义类或接口类似,如:
<?php
trait testTrait {
    //性状内部代码
}
使用use关键字引入一个性状,与引入命名空间类似,但命名空间在类外部引入,性状在类内部引入,如:
<?php
class testClass {
    use testTrait;
    //此时便可使用性状内部的属性及方法
}
```
### 注意事项
1.性状看起来既像类,也像接口,但实际上性状既不是类也不是接口,而是为了解决无法继承同一父类的两个类需要同样代码时的问题,避免过度封装.
2.可以将使用性状理解为编译时将性状的代码直接复制到引入地点,编译时不会对是否产生问题进行检查
  所以如果性状内部实现时依赖了外部的某些属性或方法,需要自行保证引入性状的类拥有这些属性或方法.

## 生成器

### 版本要求
```
5.5.0及以上
```
### 使用生成器

创建一个生成器与创建一个普通的方法类似,只不过需要使用yield关键字.如:
```php
<?php
function myTest(){
    yield 'test1';
    yield 'test2';
    yield 'test3';
}
```
生成器不返回值,只产出值,生成器可以使用foreach进行迭代产出,直到生成器末尾或遇到return语句,如:
```php
<?php
foreach(myTest() as $test){
    echo $test,PHP_EOL;
}
```
输出如下:
```
test1
test2
test3
```
### 注意事项
1.生成器是一个简单的迭代器,无需实现迭代接口,生成器是一次性的,无法多次迭代(可以重建或克隆生成器).
2.生成器无法完成复杂的迭代操作(如快进,快退等),在不查询时,生成器永远不知道下一个值是什么.
3.生成器是功能多样和简洁之间的折中方案,适合大量的数组或大型数据集等操作,比迭代器或数组的效率更高,更省内存.

原文: [http://codeape.leanote.com/post/Untitled-55192a6538f41114e800035c-13](http://codeape.leanote.com/post/Untitled-55192a6538f41114e800035c-13)