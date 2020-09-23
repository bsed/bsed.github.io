---
title: php namespace use 用法
date: 2015-10-12 20:14:00
updated: 2015-10-13 20:16:36
tags: 
- Sublime-Text3
- bash
- git
- windiows
categories: 
- js

---
现在说这个，感觉有点过时了，但是感觉用namespace的人还是不多，估计还是因为不习惯吧。
class把一个一个function组织起来，namespace可以理解成把一个一个class,function等有序的组织起来。个人觉得，namespace的主要优势有

 - 第一,可以更好的管理代码
 - 第二,文件一多，可以避免class，function的重名
 - 第三,代码可读性增强了

## 定义namespace

    namespace userCenter;
    //php代码
    
    namespace userCenter\register;
    //php代码
    
    namespace userCenter\login {
     //php代码
    }


<!--more-->


命名空间不能嵌套或在同一代码处声明多次(只有最后一次会被识别)。但是，你能在同一个文件中定义多个命名空间化的代码，比较合适的做法是每个文件定义一个命名空间（可以是相同命名空间）。

## 调用namespace

    \userCenter\register; //绝对调用
    userCenter\login; //相对调用
    use userCenter\register; //引用空间
    use userCenter\register as reg; //引用空间并加别名

## 实例说明

*login.class.php：*

    <?php
    
    namespace userCenter;
    
    function check_username(){
     echo "login OK<br>";
    }
    
    class login{
     public function save(){
     echo "login had saved<br>";
     }
    }
    ?>

*regist.class.php：*

    <?php
    namespace userCenter\regist
    {
     function check_username() {
     echo "regist OK<br>";
     } 
    
     class regist{
     public function save(){
     echo "regist had saved<br>";
     }
     }
    }
    
    ?>
*test.php：*

    <?php
    
    require "login.class.php";
    require "regist.class.php";
    
    use userCenter\regist;    //使用use调用空间
    use userCenter\regist as reg;  //as定义别名
    
    echo \userCenter\check_username();  //绝对调用
    
    $login = new \userCenter\login();
    echo $login->save();
    
    echo regist\check_username();    //相对调用
    echo reg\check_username();       //别名调用 
    
    $regist = new reg\regist();
    echo $regist->save();

使用use，比绝对调用要好一点，好比给class,function等加了一个前缀，这样看起来就比较清楚了