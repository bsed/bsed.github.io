---
title: php中的魔术方法 __set() __get()
date: 2012-12-26 17:35:00
updated: 2015-01-08 15:23:29
tags: 
- echo
categories: 
- zimbu

---
先看看[官方](http://cn.php.net/manual/en/language.oop5.overloading.php#object.get)文档的解释
__set() is run when writing data to inaccessible properties.
__get() is utilized for reading data from inaccessible properties.
究竟用中文怎么翻译呢？   inaccessible ：n. 难达到；难接近；无法理解。
有代码有真相：


<!--more-->


    <?php
    error_reporting(E_ALL);
    class stu{
    	private $a;
    	private $b = 0;
    	public $c;
    	public $d = 0;
    	
    	//这里的 private 可以用 protected public 替代
    	private function __get($name) {
    		return 123;
    	}
    	
    	//这里的 private 也可以用 protected public 替代
    	private function __set($name, $value) {
    		echo "This is set function";
    	}
    }
    
    $s = new stu();
    
    var_dump($s->a);  //output: 123
    var_dump($s->b);  //output: 123
    var_dump($s->c);  //output: null
    var_dump($s->d);  //output: 0
    var_dump($s->e);  //output: 123
    
    $s->a = 3;	//output: This is set function
    $s->c = 3;  //no output
    $s->f = 3;  //output: This is set function
    ?>

**结果：**
如果没有__get方法，执行 var_dump($s->a)  var_dump($s->b) 会有致命的错误
如果没有__get方法，执行 var_dump($s->e) 会有一个notice，提示没有定义属性 $e

**总结：**
1. 从一个难以访问的属性读取数据的时候 __get() 方法被调用
2. 向一个难以访问的属性赋值的时候 __set() 方法被调用
3. 难以访问包括：（1）私有属性，（2）没有初始化的属性
4. __isset() __unset() 也类似 