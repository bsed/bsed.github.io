---
title: 解决 react-native run-android :app:installDebug failed
date: 2017-01-06 14:51:00
updated: 2017-02-18 14:55:13
tags: 
- wget
categories: 
- linux

---
在使用react native做练习的时候环境搭建出现问题

当出现react-native run-android :app:installDebug failed 错误时表示没有android虚拟机运行

需要到sdk安装目录 运行 `AVD Manager` 找到设备 点击start 运行android模拟器

没有模拟器可以同 avd manager 创建

运行成功后在执行`react-native run-android`

或者直接使用android手机连接电脑需要打开debug模式


<!--more-->


运行成功画面

![react_native-android.png][1]


  [1]: https://imgs.gnux.cn/usr/uploads/2017/02/152825705.png