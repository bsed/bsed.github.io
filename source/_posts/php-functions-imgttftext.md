---
title: PHP学习笔记：函数 ImageTTFText()
date: 2014-12-18 14:37:00
updated: 2014-12-18 14:39:09
tags: 
- python
- static-blog
categories: 
- python

---
ImageTTFText
写 TTF 文字到图中。

语法: array ImageTTFText(int im, int size, int angle, int x, int y, int col, string fontfile, string text);


<!--more-->


返回值: 数组

函数种类: 图形处理

内容说明

本函数将 TTF (TrueType Fonts) 字型文字写入图片。参数 size 为字形的尺寸；angle 为字型的角度，顺时针计算，0 度为水平，也就是三点钟的方向 (由左到右)，90 度则为由下到上的文字；x,y 二参数为文字的坐标值 (原点为左上角)；参数 col 为字的颜色；fontfile 为字型文件名称，亦可是远端的文件；text 当然就是字符串内容了。返回值为数组，包括了八个元素，头二个分别为左下的 x、y 坐标，第三、四个为右下角的 x、y 坐标，第五、六及七、八二组分别为右上及左上的 x、y 坐标。治募注意的是欲使用本函数，系统要装妥 GD 及 Freetype 二个函数库。

使用范例

本例建立一个 400×30 pixel 大小的黑底图，并用 Arial 向量字体写出 I am NUMBER ONE !! 的白字。

    <?php
    Header(“Content-type: image/gif”);
    $im = imagecreate(400,30);
    $black = ImageColorAllocate($im, 0,0,0);
    $white = ImageColorAllocate($im, 255,255,255);
    ImageTTFText($im, 20, 0, 10, 20, $white, “/somewhere/arial.ttf”, “I am NUMBER ONE !!”);
    ImageGif($im);
    ImageDestroy($im);
    ?>

注“ImageTTFText($im, 20, 0, 10, 20, $white, “/somewhere/arial.ttf”, “I am NUMBER ONE !!”);”中 20, 0是指字体的大小，10,20分别是宽和高（用windows画图板可以看出）