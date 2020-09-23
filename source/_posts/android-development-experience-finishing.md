---
title: Android开发心得整理
date: 2016-03-27 22:35:00
updated: 2016-05-18 19:20:55
tags: 
- php
- curl
categories: 
- php

---
> 原文：[https://medium.com/@laanayabdrzak/android-development-some-of-the-best-practices-27722c685b6a#.x3b8wsvz9](https://medium.com/@laanayabdrzak/android-development-some-of-the-best-practices-27722c685b6a#.x3b8wsvz9)
> 翻译: [chengdazhi](http://blog.chengdazhi.com/)

在开发了几个项目之后我决定写篇文章分享一下一路走来的经验教训。

 - 在使用任何**第三方库**之前都要三思，这件事非常严肃。
 - 除非必须，不要使用**数据库**。
 - 你可以尝试使用**realm**（第三方数据库），这个真的不错。
 - 项目很快就会达到65k方法，真的很快。此时可以求助**Multidexing**。
 - **RxJava**是**AsyncTask**的最佳替代，而且它远不止于此。
 - Retrofit是最好用的网络库。
 - 不要自己写**Http**客户端，可以用**Volley**或**OkHttp**
 - 使用**RetroLambda**缩减代码。
 - 我能想到人生最cool的事，就是把**RxJava**、**Retrofit**和**RetroLambda**组合在一起。
 - **EventBus**挺好用，但我不会用太多，因为代码会变得很纠结。
 - 通过**功能**分包，而不是通过**层**。
 - 不要在**UI线程中**执行逻辑代码，不然可能会**ANR**。
 - 使用[Lint](http://developer.android.com/tools/debugging/improving-w-lint.html)检查Layout层级可以帮你发现没用的View，兴许可以去掉。
 - 使用**Gradle**以及默认项目结构。
 - 把密码与敏感数据放在**gradle.properties**里。（译者注：或许更好的方式是把这些数据放在local.properties里，然后把这个文件加进.gitignore）
 - 使用**styles**来避免在Layout文件中写重复代码。
 - 不要让**ViewGroup层级**太多。（会过度绘制）
 - 监控电量，**充电时**可以进行更多的数据更新，**低电量**时停止数据的自动更新。
 - 可以尝试**JobScheduler**。
 - 当**系统缺少内存**（而不是应用缺少内存）时，系统会调用onLowMemory()方法，所以OOM原则上无法避免。
 - 费电30%主要来源于（图像，动画，…），和70%（分析，广告，地图，全球定位系统）。
 - **监控连接**和**连接类型**（当在wifi条件下的时候，可以进行更多的数据更新）
 - 使用**[Account Manager](http://developer.android.com/reference/android/accounts/AccountManager.html)**来提示登录所需的信息（用户名、邮箱、密码等）。
 - 给方法一个清晰的名字，要能顾名思义。
 - **启动界面**是应用带给用户的第一体验。
 - 如果不需要**启动界面**，那不要无故添加。
 - 保持**colors.xml**文件短而简单(DRY），只写基本颜色就行。
 - 保持**dimens.xml**文件简单，定义基本常量。
 - 当要时常修改一个字符串时，使用**StringBuffer**或**StringBuilder**(后者不保证线程安全)。
 - 实际上p**erfExternal**很少用作应用程序的外部存储器停止,一旦设备被连接到一台计算机和安装USB存储
 - 为了避免**内存泄露**：
 - 不要在**AsyncCallBack**中保留**View**引用。
 - 不要让静态对象持有**View**引用。
 - 最好不要在集合框架中存储View，但你也可以使用**WeakHashMap**。
 - **FlatBuffers**是一个高效的跨平台的序列化类库，建议使用。
 - **Serializable**实现起来很方便，但性能是真的差。

先说这么多，希望对大家有所帮助。