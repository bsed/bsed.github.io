---
title: 我所使用的字体
date: 2014-08-28 08:30:00
updated: 2014-08-28 08:32:12
tags: 
- rails
- mongodb
- hash
categories: 
- rails

---
Windows操作系统：

        黑体：SimHei
        宋体：SimSun
        新宋体：NSimSun
        仿宋：FangSong
        楷体：KaiTi


<!--more-->


        仿宋GB2312：FangSongGB2312
        楷体GB2312：KaiTiGB2312
        微软雅黑：Microsoft YaHei （Windows 7开始提供）

OS X操作系统：

        冬青黑体: Hiragino Sans GB （SNOW LEOPARD开始提供）
        华文细黑：STHeiti Light （又名STXihei）
        华文黑体：STHeiti
        华文楷体：STKaiti
        华文宋体：STSong
        华文仿宋：STFangsong

如果用户装了MicroSoft Office，还会多出一些字体。

        隶书：LiSu
        幼圆：YouYuan
        华文细黑：STXihei
        华文楷体：STKaiti
        华文宋体：STSong
        华文中宋：STZhongsong
        华文仿宋：STFangsong
        方正舒体：FZShuTi
        方正姚体：FZYaoti
        华文彩云：STCaiyun
        华文琥珀：STHupo
        华文隶书：STLiti
        华文行楷：STXingkai
        华文新魏：STXinwei

二、font-family命令

CSS的font-family命令，指定了网页元素所使用的字体。下面是一个例子。


    font-family: Georgia, "Times New Roman", 
                 "Microsoft YaHei", "微软雅黑", 
                 STXihei, "华文细黑", 
                 serif;

它的规则有三条。

    （1）优先使用排在前面的字体。

    （2）如果找不到该种字体，或者该种字体不包括所要渲染的文字，则使用下一种字体。

    （3）如果所列出的字体，都无法满足需要，则让操作系统自行决定使用哪种字体。
