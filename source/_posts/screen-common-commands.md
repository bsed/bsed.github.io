---
title: Screen常用命令
date: 2014-11-20 09:16:00
updated: 2016-11-20 09:17:12
tags: 
- linux
- htop
categories: 
- linux

---
    c-a w 显示所有窗口列表
    c-a c-a 切换到之前显示的窗口
    c-a c 创建一个新的运行shell的窗口并切换到该窗口
    c-a n 切换到下一个窗口
    c-a p 切换到上一个窗口
    c-a 0..9 切换到窗口0…9
    c-a d 暂时断开screen窗口
    c-a k 杀掉当前窗口
    c-a z 把当前session放到后台执行
    c-a t Time，显示当前时间和系统的load
    C-a [ -> 进入 copy mode，在 copy mode 下可以回滚、搜索、复制就像用使用 vi 一样
    C-b Backward，PageUp
    C-f Forward，PageDown
 


<!--more-->


   H(大写) High，将光标移至左上角
    L Low，将光标移至左下角
    0 移到行首
    $ 行末
    w forward one word，以字为单位往前移
    b backward one word，以字为单位往后移
    Space 第一次按为标记区起点，第二次按为终点
    Esc 结束 copy mode
    C-a ] -> Paste，把刚刚在 copy mode 选定的内容贴上
    c-a ? 查看所有快捷键
    c-a A 设置标题
    c-a “ 查看窗口列表
    screen -ls 查看有哪些screen
    screen -r id 打开编号为id的screen窗口
    screen -wipe 清除死掉的绘画