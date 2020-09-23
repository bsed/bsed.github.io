---
title: php中的json_encode处理
date: 2013-11-05 16:39:00
updated: 2015-01-08 15:17:12
tags: 
- php
- random
categories: 
- php

---
php 中函数很多，用起来都比较顺手。正是这样，久而久之，就会形成一种只看参数名称，不仔细阅读函数用法的坏习惯。比如，其中的 json_encode ， 现在大多数网站是采用 UTF8 编码，这种情况下使用 json_encode 是没有任何问题的，但当网站编码是其他时，这就不好了，如下：

    <?php
    
    // 字符串
    $str = '我是中国人';
    $json_str = json_encode($str);
    var_dump($json_str, json_decode($json_str));
    
    // 对象
    class stu {
    	var $age = 10;
    	var $name = '老外';
    }
    $s1 = new stu();
    $json_s1 = json_encode($s1);
    var_dump($json_s1, json_decode($json_s1));
    ?>


<!--more-->


如果上面文件保存为 **UTF8** 编码时，结果完全正确，如果保存为其他编码时，结果不是你期望的。
其实这个情况，在手册中是有说明的，如下：
待编码的 value ，除了resource 类型之外，可以为任何数据类型
该函数只能接受 UTF-8 编码的数据（译注：指字符/字符串类型的数据）

那么，如果我们的编码不是 UTF8 时就完全不能使用这个功能吗？可以，需要稍加处理，如下：

    define('DEFAULT_CHARSET', 'gbk');
    
    function json_safe_encode($var)
    {
        return json_encode(json_fix_cyr($var));
    }
    
    function json_fix_cyr($var)
    {
        if (is_array($var)) {
            $new = array();
            foreach ($var as $k => $v) {
                $new[json_fix_cyr($k)] = json_fix_cyr($v);
            }
            $var = $new;
        } elseif (is_object($var)) {
            $vars = get_class_vars(get_class($var));
            foreach ($vars as $m => $v) {
                $var->$m = json_fix_cyr($v);
            }
        } elseif (is_string($var)) {
            $var = iconv(DEFAULT_CHARSET, 'utf-8', $var);
        }
        return $var;
    } 

上面的例子是假设是 gbk 编码的情况，如果是其他编码情况，聪明的你一定知道该怎么搞了吧~~