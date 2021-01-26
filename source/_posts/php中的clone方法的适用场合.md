---
title: "php中的clone方法的适用场合"
categories: [ "PHP" ]
tags: [ "php","clone" ]
draft: false
slug: "php-function-clone"
date: "2012-09-18 17:21:00"
---

在php语言中，对象之前相互传递是传其引用，如下：

    <?php
    class stu {
    	public $age;
    }
    
    $s1 = new stu();
    $s1->age = 2;
    
    $s2 = $s1;
    $s2->age = 3;
    
    echo $s1->age;  //output 3
    ?>


<!--more-->


从上面可以看出，$s1,$s2 指向是相同的，修改其中一个的属性值，另一个对应的属性值也在跟着变。普通的变量则是直接重新拷贝一份给其传递，如果对象之间想要实现这种结果，可以的，这就是clone的作用了。如下：

    <?php
    class stu {
    	public $age;
    }
    
    $s1 = new stu();
    $s1->age = 2;
    
    $s2 = clone $s1;
    $s2->age = 3;
    
    echo $s1->age;  //output  2
    ?>

综上所述，如果想复制一个对象，那就用 clone（克隆）吧，普通的 等号(=) 赋值不是真正的复制，而是把指针指到同一块区域了。