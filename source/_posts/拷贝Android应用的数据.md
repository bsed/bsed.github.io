---
title: "拷贝Android应用的数据"
categories: [ "Android" ]
tags: [ "android" ]
draft: false
slug: "copy-android-app-data"
date: "2013-05-14 07:23:00"
---

# 有root权限


<!--more-->


```
adb shell su -c cat /data/data/app.package.name/databases/application.sqlite | sed 's/\r$//' > application.sqlite
```

# 应用可调试的话
```
adb shell
run-as app.package.name \
cp /data/data/package.name/databases/application.sqlite /sdcard/
exit
adb pull /sdcard/application.sqlite ~/
```


# 使用备份方法
```
adb backup -f ~/data.ab -noapk app.package.name
dd if=data.ab bs=1 skip=24 | python -c "import zlib,sys;sys.stdout.write(zlib.decompress(sys.stdin.read()))" | tar -xvf -
```

# 参考链接
1. http://blog.shvetsov.com/2013/02/access-android-app-data-without-root.html