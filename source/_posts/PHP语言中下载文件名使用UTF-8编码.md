---
title: "PHP语言中下载文件名使用UTF-8编码"
categories: [ "PHP" ]
tags: [ "php","download","utf8" ]
draft: false
slug: "php-download-name-use-utf8"
date: "2012-10-25 15:35:00"
---

即：
 - filename后面的等号之前要加 *

 - filename的值用单引号分成三段，分别是字符集(utf8)、语言(空)和urlencode过的文件名。

 - 最好加上双引号，否则文件名中空格后面的部分在Firefox中显示不出来

 - 注意urlencode的结果与php的urlencode函数结果不太相同，php的urlencode会把空格替换成+，而这里需要替换成%20

经过试验，发现几种主流浏览器的支持情况如下：

> IE6	attachment; filename=""
> 
> FF3	attachment; filename="UTF-8文件名"
> 
> ~	attachment; filename*="utf8''"
> 
> O9	attachment; filename="UTF-8文件名"
> 
> Safari3(Win)	貌似不支持？上述方法都不行


这样看来，程序必须得这样写才能支持所有主流浏览器：
   

    <?php
        
        $ua = $_SERVER["HTTP_USER_AGENT"];
        
        $filename = "中文 文件名.txt";
        $encoded_filename = urlencode($filename);
        $encoded_filename = str_replace("+", "%20", $encoded_filename);
        
        header('Content-Type: application/octet-stream');
        
        if (preg_match("/MSIE/", $ua)) {
            header('Content-Disposition: attachment; filename="' . $encoded_filename . '"');
        } else if (preg_match("/Firefox/", $ua)) {
            header('Content-Disposition: attachment; filename*="utf8\'\'' . $filename . '"');
        } else {
            header('Content-Disposition: attachment; filename="' . $filename . '"');
        }
        
        print 'ABC';
        ?>