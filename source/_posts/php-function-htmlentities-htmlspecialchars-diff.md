---
title: php中的htmlentities与htmlspecialchars的区别
date: 2012-10-07 17:15:00
updated: 2015-01-08 15:24:58
tags: 
- css
- 自动适配
categories: 
- css

---
php官方手册的解释：
htmlentities() 函数把字符转换为 HTML 实体。
htmlspecialchars() 函数把一些预定义的字符转换为 HTML 实体。

下面是作者的理解，如有差错，请您指明。
htmlentities 与 htmlspecialchars 在处理英文字符的时候完全一样，在处理中文字符的时候就有区别啦。
htmlentities 将会对几个特殊字符进行转换，但是遇到中文的时候，如果文件的编码与第三个参数不同就会出现乱码。
而htmlspecialchars 却只可能转换如下几个特殊字符：

    & （和号） 成为 &amp;
    " （双引号） 成为 &quot;
    ' （单引号） 成为 &#039;
    < （小于） 成为 &lt;
    > （大于） 成为 &gt;

因此，对于中文不会出现乱码。请看以下代码对比：

    <?php
    $str='<a href="test.html">测试页面</a>';
    $str1 = htmlentities($str);
    echo $str1;   //出现乱码
    $str2 = htmlspecialchars($str);
    echo $str2;   //显示正常
    $str3 = htmlentities($str, ENT_COMPAT, 'utf-8');
    echo $str3;   //显示正常
    ?>


<!--more-->


以上文件编码是：utf-8
在大多数情况下，我们都不需要对中文进行处理，所以在转换html代码的时候还是建议使用htmlspecialchars()，这样可以少些两个参数，呵呵！