---
title: parseInt的陷阱
date: 2012-11-04 11:49:00
updated: 2015-11-07 11:50:34
tags: 
- gulp
- nodejs
categories: 
- js

---
    var a = parseInt("09"), b = Number("09");

很多人会认为a和b的值都是数字9，但实际上不是。

parseInt的主要作用是把字符串转换为整数，或者把小数转换为整数。一般情况下，我们只用到它的第一个参数。但实际上，它有两个参数：

    parseInt(string, radix)


<!--more-->


parseInt会根据radix指定的进制进行转换，比如：

    alert(parseInt("10", 2)); // outputs '2'

在没有指定radix或者radix为0的情况下，parseInt会按十进制进行转换。然而，这在某些情况下有点特殊：

    如果string的值以“0x”开头，parseInt会按十六进制进行转换；
    如果string的值以“0”开头，parseInt会按八进制进行转换。

说回开头的代码，由于"09"是以“0”开头，所以parseInt会按八进制进行转换，但是“9”不是合法的八进制值（八进制只有0－7八个数字），所以转换结果是0。

要避免这个陷进，可以强制指定radix：

    alert(parseInt("09", 10)); // outputs '9'