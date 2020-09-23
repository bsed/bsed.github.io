---
title: 环信 Android 点击聊天中的图片导致崩溃
date: 2017-09-07 10:01:00
updated: 2017-09-06 09:00:02
tags: 
- golang
- io
categories: 
- go

---
解决方法，在 `app/src/main/AndroidManifest.xml` 中添加
```
<activity android:name="com.hyphenate.easeui.ui.EaseShowBigImageActivity" />
```
## 总结
Android 在引入三方库的时候，如果三方库中使用的其他 Activity 依然需要在 AndroidManifest.xml 中进行声明。

感觉这非常之不合理。

原文：[环信 Android 点击聊天中的图片导致崩溃](http://www.sunzhongwei.com/android-ring-letter-click-on-the-pictures-in-chat-lead-to-collapse)