---
title: emacs 中的日程管理
date: 2012-05-16 21:31:00
updated: 2015-07-20 21:33:10
tags: 
- firefox
- debug
categories: 
- css

---
 手册

    M-x man

打开日历,设置日历以周几为第一天

    M-x calendar
    variable calendar-week-start

移动一个月，三个月，一年

    M-{ M-}
    C-v M-v
    C-x [ C-x ]

<!--more-->


移动到周首末，月首末，年首末

    C-a C-e
    M-a M-e
    M-< M->

左右滚动日历

    C-x < C-x >

移动到指定日

    g d

查询三个月内的节假日，查询当日节假日信息，高亮显示/隐藏节假日

    a
    h

    x
    u

按日周月年插入日程，插入周年日程，插入循环日程

    i d
    i w
    i m
    i y
    i a
    i c
    i b

查看当日日程，查看日程文件

    d
    s

查看当天是一年当中的第几天和当年还剩多少天，查看其他日历

    p d
    p o

查看月相,查看日出日落时间

    M
    S

查看当前选中区域的天数

    M-=
