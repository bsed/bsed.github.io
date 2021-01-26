---
title: "[解决问题]Cordova : Command failed with exit code 1"
categories: [ "Css" ]
tags: [ "cordova","ionic","app","errors" ]
draft: false
slug: "cordova-failed-exit-code-with-1"
date: "2015-08-08 13:31:00"
---

> OS: Ubuntu 15.4
> Cordova : 5.1.1 
> Ionic: 1.6.4

# 错误一：Command failed with exit code 1

    Error: /root/workspace/web/myApp/platforms/ios/cordova/build: Command failed with exit code 1
        at ChildProcess.whenDone (/usr/local/node/lib/node_modules/cordova/node_modules/cordova-lib/src/cordova/superspawn.js:134:23)
        at ChildProcess.emit (events.js:110:17)
        at maybeClose (child_process.js:1015:16)
        at Process.ChildProcess._handle.onexit (child_process.js:1087:5)

如图：
![2015-08-08 13.png][1]


<!--more-->


**解决方法**：

    chmod -R 777 ~/.cordova/lib/npm_cache/cordova-android/4.0.2/package/bin

# 错误二： android_home 环境变量未找到

    [Error: Android SDK not found. Make sure that it is installed. If it is not at the default location, set the ANDROID_HOME environment variable.]

**解决方法**：
`sudo vim ~/.bashrc`
在下面添加如下配置：

    #jdk1.8
    export JAVA_HOME=/opt/java/jdk/jdk1.8.0_45
    export JRE_HOME=${JAVA_HOME}/jre
    export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
    export PATH=${JAVA_HOME}/bin:$PATH
    #android dev path
    export ANDROID_HOME=~/tools/adt-bundle-linux-x86_64-20140702/sdk
    export PATH=${PATH}:~/tools/adt-bundle-linux-x86_64-20140702/sdk/tools
    export PATH=${PATH}:~/tools/adt-bundle-linux-x86_64-20140702/sdk/platform-tools

# 错误三：android 开发版本不匹配
由于我的android skd版本是４．４　导致出现一下问题，我只能呵呵了  ，另外．Android SDK Platform-tools　和ｂuild-tools　两个包必须是最新的

    [Error: Please install Android target: "android-22".
    
    Hint: Open the SDK manager by running: /root/tools/adt-bundle-linux-x86_64-20140702/sdk/tools/android
    You will require:
    1. "SDK Platform" for android-22
    2. "Android SDK Platform-tools (latest)
    3. "Android SDK Build-tools" (latest)]

# 错误四：不能发现　模块`Ｑ`

    module.js:338
        throw err;
              ^
    Error: Cannot find module 'Q'
        at Function.Module._resolveFilename (module.js:336:15)
        at Function.Module._load (module.js:278:25)
        at Module.require (module.js:365:17)
        at require (module.js:384:17)
        at Object.<anonymous> (/root/workspace/web/myApp/platforms/ios/cordova/lib/check_reqs.js:25:13)
        at Module._compile (module.js:460:26)
        at Object.Module._extensions..js (module.js:478:10)
        at Module.load (module.js:355:32)
        at Function.Module._load (module.js:310:12)
        at Module.require (module.js:365:17)

解决方法：

将这个位置下的`/root/workspace/web/myApp/platforms/ios/cordova/lib/check_reqs.js` 中的`var Q = require('Q') `替换成 `var Q = require('q')`
应该算笔误吧

[1]: https://imgs.gnux.cn/usr/uploads/2015/08/4287214618.png