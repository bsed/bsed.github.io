---
title: php中的自定义随机字符串的生成方法
date: 2013-05-13 17:07:00
updated: 2015-01-08 15:19:38
tags: 
- SWOOLE
categories: 
- php

---
代码：

    function generate_custom_key($length = 8 ) {
    	$chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!@#$%^&*()-_ []{}<>~`+=,.;:/?|';
    	$charslen = strlen($chars);
    	$str = '';
    	for($i = 0; $i < $length; $i++ ) {
    		$str .= $chars[mt_rand(0, $charslen - 1)];
    	}
    	return $str;
    }

mt_rand() 使用 Mersenne Twister 算法返回随机整数。
[http://www.w3school.com.cn/php/func_math_mt_rand.asp](http://www.w3school.com.cn/php/func_math_mt_rand.asp)
