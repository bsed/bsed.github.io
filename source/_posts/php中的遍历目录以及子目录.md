---
title: "php中的遍历目录以及子目录"
categories: [ "PHP" ]
tags: [ "php","foreach" ]
draft: false
slug: "php-function-foreach-dir-filename"
date: "2011-10-18 17:52:00"
---

源码如下：

    <?php 
    $dir = "c:/wamp/www/test/";  //目录名称必须是这种格式
    $space="";
    function fun1($dir,$space="") {
        if(is_dir($dir) && $handle = opendir($dir)){
            $space .= "&nbsp;&nbsp;&nbsp;&nbsp;";
            while(false!== ($file = readdir($handle))){
                if($file=="." || $file=="..") continue;
                if(is_dir($dir.$file.'/')){
                    echo "$space|-$file<br>";
                    fun1($dir.$file.'/',$space);
                }else
                echo "$space|-$file<br>";
            }
            closedir($handle);
        }else{
            echo "This have a error!";
        }
    }
    fun1($dir);
    ?>


<!--more-->


遍历效果如下：
 |-1.php
    |-2.php
    |-2010-05-22_101859.jpg
    |-321.html
    |-ci
        |-.htaccess
        |-index.php
        |-system