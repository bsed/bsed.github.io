---
title: "php中的filesize"
categories: [ "PHP" ]
tags: [ "php","filesize" ]
draft: false
slug: "php-function-filesize"
date: "2012-08-14 17:12:00"
---

ilesize — 取得文件大小 
int filesize ( string $filename )

注意事项：
1> 对于路径中使用了“\”分隔上级目录与子目录的文件,或者是路径中包含中文字符都将会出现


<!--more-->


如下：

    <?php
    var_dump(filesize("E:\123.txt"));
    ?>

Warning: filesize() [function.filesize]: stat failed for........
2> $filename参数可以是绝对路径，也可是相对路径
3> 对于官方手册中提到的：
Note: 因为 PHP 的整数类型是有符号的，并且大多数平台使用 32 位整数，filesize() 函数在碰到大于 2GB 的文件时可能会返回非预期的结果。对于 2GB 到 4GB 之间的文件通常可以使用 sprintf("%u", filesize($file)) 来克服此问题。 
ps : 这点初步测试结果是不会出错