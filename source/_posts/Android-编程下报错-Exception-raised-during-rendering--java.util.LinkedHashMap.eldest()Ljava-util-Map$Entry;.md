---
title: "Android 编程下报错 Exception raised during rendering: java.util.LinkedHashMap.eldest()Ljava/util/Map$Entry;"
categories: [ "Android" ]
tags: [ "android" ]
draft: false
slug: "android-programming-under-raised-exception-during-rendering-ljavautilmapentry-javautillinkedhashmapeldest"
date: "2015-10-01 18:03:00"
---

Android 的 ADT 版本升级到 ADT 23.0 后，当我们尝试向布局文件中添加 EditText 控件时会发现布局文件不能正常显示，同时会报错 `Exception raised during rendering: java.util.LinkedHashMap.eldest()Ljava/util/Map$Entry`; 临时解决方法如下：在 `EditText` 的属性中加入` android:inputType="textNoSuggestions"` 即可。
错误如图：
![yigrherb_Exception raised during rendering.jpg][1]
<!--more-->


*activity_main.xml:*
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textNoSuggestions" />

</LinearLayout>
```


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/614030972.jpg