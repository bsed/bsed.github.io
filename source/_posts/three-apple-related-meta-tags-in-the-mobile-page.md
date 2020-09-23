---
title: 移动页面中的三个apple相关meta标签
date: 2015-09-28 20:36:00
updated: 2016-06-02 11:31:51
tags: 
- android
categories: 
- java

---
在ios8的Safari中测试以下meta标签

## 标签一：

`<meta name="apple-mobile-web-app-capable" content="yes">`

意思是设置web应用是否运行在全屏模式
自己做了个demo，无论设置为yes或no，似乎都没什么变化，打印window.navigator.standalone，一直是false。


<!--more-->


## 标签二：

`<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">`

意思是：在全屏模式下，可设置状态栏的颜色，可以为default,black,black-translucent三种值

继续在上面的demo里加入这个设置，无论哪个值，又似乎没什么变化

## 标签三：
`<meta name="format-detection" content="telephone=no">`
意思是：可设置文本的一些格式化,比如电话号码自动识别，邮件自动识别。默认是开启识别功能的。

经测试，邮件似乎无法识别，电话号码可以，但只有【初始】文档中的一些数字，才可被自动识别为电话号码，通过js后期插入到DOM中的数字是无法识别的，通过weinre调试得知，Safari自动为电话号码包裹上了a标签，并设置了href="tel:{电话号码}"。请看图：
图片丢失:>
当设置content="telephone=no"，自动识别将关闭。

不知是不是我测试方法不对呢，还是随着iOS或Safari的版本升级，导致部分功能失效。
