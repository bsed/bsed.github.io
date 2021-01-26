---
title: "Weex android 避免切换页面时弹出应用选择框"
categories: [ "Android" ]
tags: [ "weex" ]
draft: false
slug: "weex-android-pops-up-the-application-selection-box-when-switching-pages"
date: "2017-09-03 08:42:00"
---

当系统中安装了多个 Weex 开发的 Android APP 时，如果使用 `navigator.push` 来切换页面，会弹出一个应用选择框，即使用哪个 APP 来打开目标页面。

出现这个问题的原因是，WEEX 使用的是隐式的 `intent filter`, 而注册的 `intent category` 都是写死的。所以，这些 WEEX 应用在 Android 系统中都注册了同样的 category

目前使用的糙快猛的解决方案，直接改 SDK 源码。。。


<!--more-->


platforms/android/app/src/main/AndroidManifest.xml
```
-  <category android:name="com.taobao.android.intent.category.WEEX"/>
+ <category android:name="com.zljypatient.android.intent.category.WEEX"/>
```
platforms/android/sdk/src/main/java/com/taobao/weex/appfram/navigator/WXNavigatorModule.java

```
- private final static String WEEX = "com.taobao.android.intent.category.WEEX";
+ private final static String WEEX = "com.zljypatient.android.intent.category.WEEX";
```
platforms/android/app/src/main/java/com/alibaba/weex/SplashActivity.java 以及 platforms/android/app/src/main/java/com/jindong/extend/LaunchActivity.java
```
- intent.addCategory("com.taobao.android.intent.category.WEEX");
+ intent.addCategory("com.zljypatient.android.intent.category.WEEX");
```

原文：[Weex Android 避免切换页面时弹出应用选择框](http://www.sunzhongwei.com/weex-android-avoid-switch-page-pops-up-application-selection-box)