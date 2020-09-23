---
title: Weex android 支持 base64
date: 2017-09-05 08:54:00
updated: 2017-09-06 08:51:58
tags: 
- golang
- file
categories: 
- go

---
Weex Android 默认情况下不支持 Base64 的图片显示，会显示成空白。

需要手动修改 WXApplication.java 的代码以支持。

原理是，Weex Android 默认的 ImageAdapter 是用的 Picasso，而 Picasso 默认不支持 Base64。 所以，切换为 FrescoImageAdapter 即可。

```
--- a/platforms/android/app/src/main/java/com/alibaba/weex/WXApplication.java
+++ b/platforms/android/app/src/main/java/com/alibaba/weex/WXApplication.java
@@ -2,7 +2,8 @@ package com.alibaba.weex;
 
 import android.app.Application;
 
-import com.alibaba.weex.commons.adapter.ImageAdapter;
+import com.alibaba.weex.commons.adapter.FrescoImageAdapter;
@@ -22,7 +23,7 @@ public class WXApplication extends Application {
     WXSDKEngine.addCustomOptions("appGroup", "WXApp");
     WXSDKEngine.initialize(this,
         new InitConfig.Builder()
-            .setImgAdapter(new ImageAdapter())
+            .setImgAdapter(new FrescoImageAdapter())
             .build()
     );


```


<!--more-->


## 参考

- [Learn Weex The Hard Way (如何在 Weex 中使用图标) | 三关茅庐](http://kevin.doyeden.com/2017/03/08/learn-weex-the-hard-way-3/#base64)