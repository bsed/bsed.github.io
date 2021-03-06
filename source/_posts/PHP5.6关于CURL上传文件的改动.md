---
title: "PHP5.6关于CURL上传文件的改动"
categories: [ "PHP" ]
tags: [ "php","curl" ]
draft: false
slug: "php56-changes-on-the-curl-upload-files"
date: "2015-08-06 10:01:00"
---

## 简介

最近使用CURL的文件上传参数，发现坑一处，特此记录。
## CURL的修改

PHP在5.5版本之前，使用CURL上传文件时采用@+文件路径的方式。
但在5.5开始，上传文件引入了新的CURLFile类进行更多的处理。
好在5.5时传统办法还是可以用的，可是在5.6版本中，必须使用新的方式，老方法不再兼容。
从而可能导致老代码出现文件上传不成功的错误。
## 版本判断

判断PHP版本从而进行不同的操作是应对这一改变的有效办法。
但是不建议使用版本号进行判断，这样在后续修改中很难看出这步操作到底为了解决什么问题。
推荐使用class_exists进行判断。
```php
if(class_exists('\CURLFile')) {
    new\CURLFile($path);
}else{
    '@'. $path;
}
```
## 注意

以上写法应用于PHP5.3及以上，5.2版本由于没有命名空间，所以写的时候去掉`\`即可。