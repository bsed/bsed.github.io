---
title: android:configChanges=&quot;orientation|keyboardHidden&quot;的使用
date: 2013-06-13 22:07:00
updated: 2016-06-13 22:09:09
tags: 
- javascript
- typescript
- Declaration
- Merging
categories: 
- js

---
今天看到一个Android应用的源码，发现有一段这样的代码，平时开发的时候很少用的，后来了解到如果使用了这个属性，当横竖屏切换的时候会直接调用onCreate方法中的onConfigurationChanged方法，而不是重新执行onCreate方法，如果没有使用这个属性就会重新调用onCreate方法了。
```
<activity android:theme="@style/ContentOverlay" android:name=".BlogActivity" android:configChanges="orientation|keyboardHidden"></activity>
``