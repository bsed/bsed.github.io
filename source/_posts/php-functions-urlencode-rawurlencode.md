---
title: php中的urlencode与rawurlencode
date: 2013-11-14 16:46:00
updated: 2015-01-08 15:18:17
tags: 
- php
- filesize
categories: 
- php

---
urlencode 除了 `-_.`之外的所有非字母数字字符都将被替换成百分号（%）后跟两位十六进制数，但是空格则编码为加号（+）。
rawurlencode  除了 `-_.` 之外的所有非字母数字字符都将被替换成百分号（%）后跟两位十六进制数。

在使用中还有些细微的区别，下面看一段代码：

    <?php
    $query1 = 'foo=bar&php=hypertext+processor';
    $query2 = 'foo=bar&php=hypertext processor';
    
    echo 'query1 : '.$query1, '<br>';
    echo 'query2 : '.$query2, '<br><br>';
    
    echo 'urlencode : '.urlencode($query1), '<br>';
    echo 'urlencode : '.urlencode($query2), '<br><br>';
    
    echo 'rawurlencode : '.rawurlencode($query1), '<br>';
    echo 'rawurlencode : '.rawurlencode($query2), '<br><br>';

上面的代码将会输出如下的内容：


<!--more-->


    query1 : foo=bar&php=hypertext+processor
    query2 : foo=bar&php=hypertext processor
    
    urlencode : foo%3Dbar%26php%3Dhypertext%2Bprocessor
    urlencode : foo%3Dbar%26php%3Dhypertext+processor
    
    rawurlencode : foo%3Dbar%26php%3Dhypertext%2Bprocessor
    rawurlencode : foo%3Dbar%26php%3Dhypertext%20processor

可见，urlencode 把空格转为了 + ，rawurlencode 却是把空格转为了 %20 ， 这里看出 **urlencode 把空格做了特殊处理**。

再进行一个测试，把如下参数加在 url 后面 a=foo%3Dbar%26php%3Dhypertext%2Bprocessor  ，这个字符串就是上面的query1 经过 urlencode 之后的结果，接着加入如下测试代码，看看情况：

    <?php
    echo '$_GET[a] : '.$_GET['a'], '<br>';
    echo 'urldecode : '.urldecode($_GET['a']), '<br>';
    echo 'rawurldecode : '.rawurldecode($_GET['a']), '<br>';
    ?>

上面的代码将会输出如下的内容：

    $_GET[a] : foo=bar&php=hypertext+processor
    urldecode : foo=bar&php=hypertext processor
    rawurldecode : foo=bar&php=hypertext+processor

可见，**浏览器自动直接对参数进行了 decode 操作**。如果我们按照常理在刚开始进行了 encode 编码，后面肯定是需要 decode 解码，但是如果在这时候解码，你会发现使用 urldecode 与 rawurldecode 的结果是不同的。

接着把 query2 经过urlencode 的结果放到 url 的参数中测试，a=foo%3Dbar%26php%3Dhypertext+processor ，结果如下：

    $_GET[a] : foo=bar&php=hypertext processor
    urldecode : foo=bar&php=hypertext processor
    rawurldecode : foo=bar&php=hypertext processor

可见，浏览器自动进行了 decode 解码操作，并且是 urldecode ，而不是 rawurldecode 。为什么呢？因为 rawurldecode 是不会把 + 转换为空格的。

然后分别把query1 query2  经过rawurlencode 的结果放到url 参数中测试，发现结果都是在我们的预料之中。

总结，（1）如果要把一些特殊字符放到浏览器的 url 参数中，需要使用 encode 方式来编码，否则取到的值有可能不是你所想要的，到底用哪一个，这个看情况而定吧，一般建议使用 rawurlencode ；通过浏览器访问这些经过编码处理的 url 地址时，在接受这些参数值的时候是不需要 decode 操作的。（2）如果情况出现在与浏览器无关的场合，则需要把encode /decode 成对使用。切记，encode 的时候不要对整个参数列表做 encode，而是只对参数值进行 encode 即可。