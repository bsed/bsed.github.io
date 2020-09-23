---
title: Weex picker 在 android 上崩溃或者无响应的问题
date: 2017-09-07 08:56:00
updated: 2017-09-06 08:55:32
tags: 
- golang
- string
categories: 
- go

---
解决 picker 在 Android 上崩溃的问题是目前为止，最让我痛苦的一个 Weex 问题。

起初以为是 Weex SDK 的问题，于是将 Weex SDK 从 0.10 升级到了 0.13，但是只是解决了崩溃的问题， 变成了无响应（0.13 里只是把异常包住了而已）。

后来，我又通过源码编译的方式，把 Weex SDK 0.15-dev 集成了进来。。。问题依旧。报错信息永远是


<!--more-->


```
D/weex: callJS >>>> instanceId:1function:callJS tasks:[{"data":"1","type":2},{"data":"[{\"args\":[\"98\",\"click\",{\"position\":{\"height\":96.875,\"width\":280.20834,\"x\":234.375,\"y\":666.6667}},null],\"method\":\"fireEvent\"}]","type":3}]
D/weex: [WXBridgeManager] callNativeModule >>>> instanceId:1, module:picker, method:pick, arguments:[{"index":0,"items":["Saab","Volvo","BMW"]},"41"]
W/ResourceType: No package identifier when getting value for resource number 0x00000000
W/System.err: android.content.res.Resources$NotFoundException: Resource ID #0x0
W/System.err:     at android.content.res.Resources.getValue(Resources.java:1147)
W/System.err:     at android.content.res.Resources.loadXmlResourceParser(Resources.java:2350)
W/System.err:     at android.content.res.Resources.getLayout(Resources.java:963)
W/System.err:     at android.view.LayoutInflater.inflate(LayoutInflater.java:395)
W/System.err:     at android.view.LayoutInflater.inflate(LayoutInflater.java:353)
W/System.err:     at android.support.v7.app.AlertController$AlertParams.createListView(AlertController.java:877)
W/System.err:     at android.support.v7.app.AlertController$AlertParams.apply(AlertController.java:854)
W/System.err:     at android.support.v7.app.AlertDialog$Builder.create(AlertDialog.java:883)
W/System.err:     at com.taobao.weex.appfram.pickers.WXPickersModule.performSinglePick(WXPickersModule.java:229)
W/System.err:     at com.taobao.weex.appfram.pickers.WXPickersModule.pick(WXPickersModule.java:85)
W/System.err:     at java.lang.reflect.Method.invokeNative(Native Method)
W/System.err:     at java.lang.reflect.Method.invoke(Method.java:525)
W/System.err:     at com.taobao.weex.bridge.MethodInvoker.invoke(MethodInvoker.java:46)
W/System.err:     at com.taobao.weex.bridge.NativeInvokeHelper$1.run(NativeInvokeHelper.java:48)
W/System.err:     at com.taobao.weex.common.WXThread$SafeRunnable.run(WXThread.java:49)
W/System.err:     at android.os.Handler.handleCallback(Handler.java:730)
W/System.err:     at android.os.Handler.dispatchMessage(Handler.java:92)
W/System.err:     at android.os.Looper.loop(Looper.java:137)
W/System.err:     at android.app.ActivityThread.main(ActivityThread.java:5473)
W/System.err:     at java.lang.reflect.Method.invokeNative(Native Method)
W/System.err:     at java.lang.reflect.Method.invoke(Method.java:525)
W/System.err:     at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:875)
W/System.err:     at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:691)
W/System.err:     at dalvik.system.NativeStart.main(Native Method)

```

起初根据 StackOverflow 的搜索结果，以为是 WXPickersModule.performSinglePick 中的下面这行代码没有传入 theme 参数所致

```
final AlertDialog dialog =  new AlertDialog.Builder(mWXSDKInstance.getContext())

```

但是通过加断点调试，发现通过 getContext 已经获取到了 theme。所以，并不是 getcontext 的问题。

继续打断点，发现 android.support.v7.app.AlertDialog$Builder.create 函数

```
alertdialog mtheme 0

```

传入的 mtheme 参数为 0。于是，查了半天 Android 的资料，推测是对应的主题配置有问题。

解决问题，实际上只用了一行代码

```
--- a/platforms/android/app/src/main/AndroidManifest.xml
+++ b/platforms/android/app/src/main/AndroidManifest.xml
@@ -46,7 +46,8 @@
                 android:name=".WXPageActivity"
                 android:label="@string/app_name"
                 android:screenOrientation="portrait"
-                android:theme="@android:style/Theme.NoTitleBar">
+                android:theme="@style/AppTheme.NoActionBar">

```

picker 崩溃的原因是 platforms/android/app/src/main/res/values/styles.xml 里，根本没有 Theme.NoTitleBar 这个主题

```
    <style name="AppTheme.NoActionBar">
        <item name="windowActionBar">false</item>
        <item name="windowNoTitle">true</item>
    </style>

    <style name="AppTheme.AppBarOverlay" parent="ThemeOverlay.AppCompat.Dark.ActionBar"/>
    <style name="AppTheme.PopupOverlay" parent="ThemeOverlay.AppCompat.Light"/>

```

原文：[](http://www.sunzhongwei.com/weex-picker-on-android-collapse-or-no-response)