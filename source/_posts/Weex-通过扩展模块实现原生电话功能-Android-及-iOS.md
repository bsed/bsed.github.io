---
title: "Weex 通过扩展模块实现原生电话功能 Android 及 iOS"
categories: [ "JS" ]
tags: [ "android","ios" ]
draft: false
slug: "weex-implements-native-phone-functions-through-extended-modules-android-and-ios"
date: "2017-09-07 08:50:00"
---

WEEX 默认不支持 iOS Android 原生的电话功能，需要自己扩展模块。以下是实现方法

## iOS 的实现

Source 目录下新建 Modules 目录

JDCall.h


<!--more-->


```
#ifndef JDCall_h
#define JDCall_h

#import <WeexSDK/WXModuleProtocol.h>

@interface JDCallModule : NSObject <WXModuleProtocol>

- (void)call:(NSString *)tel;

@end

#endif /* JDCall_h */

```

JDCall.m

```
#import <Foundation/Foundation.h>
#import "JDCall.h"
#import <WeexSDK/WXSDKManager.h>
#import <WeexSDK/WXUtility.h>

@implementation JDCallModule

@synthesize weexInstance;

WX_EXPORT_METHOD(@selector(call:))

- (void)call:(NSString *)tel {
    NSString *phoneCallNum = [NSString stringWithFormat:@"telprompt://%@", tel];
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:phoneCallNum]];
}

@end

```

AppDelegate.m

```
#import "JDCall.h"

// [WeexSDKManager setup]; 下添加
[WXSDKEngine registerModule:@"jd-call" withClass:[JDCallModule class]];

```

## Android 中的实现

JDCallModule.java

```
package com.alibaba.weex;

import android.content.Intent;
import android.net.Uri;

import com.taobao.weex.WXSDKInstance;
import com.taobao.weex.common.WXModule;
import com.taobao.weex.common.WXModuleAnno;

/**
 * Created by zhongwei on 17-7-11.
 */

public class JDCallModule extends WXModule {

    @WXModuleAnno(runOnUIThread = true)
    public void call(String tel) {
        Intent callIntent = new Intent(Intent.ACTION_CALL);
        callIntent.setData(Uri.parse("tel:" + tel));
        mWXSDKInstance.getContext().startActivity(callIntent);
    }
}

```

platforms/android/app/src/main/AndroidManifest.xml

增加电话权限

```
<uses-permission android:name="android.permission.CALL_PHONE" />

```

platforms/android/app/src/main/java/com/alibaba/weex/WXApplication.java

WXSDKEngine.registerModule("event", WXEventModule.class); 下加入电话模块注册

```
WXSDKEngine.registerModule("jd-call", JDCallModule.class);

```

## Vue 中的调用方法

```
<div class="callimg" @click="call(num)">
</div>

// script methods 中实现
const JDCall = weex.requireModule('jd-call')

call: function(tel) {
    DCall.call(tel)
}
```

原文：http://www.sunzhongwei.com/weex-through-extension-module-to-realize-the-native-android-and-ios-phone-function