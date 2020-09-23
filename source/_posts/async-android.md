---
title: Android异步编程
date: 2013-09-03 12:34:00
updated: 2014-12-25 15:16:32
tags: 
- play1-x
- play教程
categories: 
- java

---
# Android的线程和内存模型

Android操作系统在boot后，会启动一个Zygote(受精卵)进程，Zygote进程负责创建大部分应用程序进程。Zygote进程启动加载核心程序库和数据结构到内存后会创建一个Dalvik虚拟机（DVM）进程－-SystemServer，此进程会包含大部分的系统服务（包括管理Activity的服务ActivityManagerService），SystemServer初始化后，Zygote进程会侦听本地的socket端口, 等待进一步的指令。当新的app被启动时，Zygote会为这个app创建一个DVM----直接fork出一个子进程，这种架构的好处是同时启动多个App时，多个App进程可以访问共享内存。

<!--more-->

<img src="https://https://imgs.gnux.cn/usr/uploads/2014/11/1957821310.png" />

Android App的进程也是一个DVM，内部有许多线程在执行，比如，主UI线程（Main Thread），垃圾回收线程等。其中主UI线程负责执行我们写的应用代码。对于只做很少的I/O操作或耗时操作的App，单一线程开发模式问题不大，但是如果有大量IO或者CPU计算的任务，我们就必须在其他线程内完成了。

因为主UI线程需要根据硬件刷新率[^3]同步用户界面的重绘。手机应用体验流畅要求界面帧率[^3]达到每秒60，也就是说每16.67毫秒就需要重绘一帧，这意味着如果我们在主线程上执行的任务超过16毫秒，就会出现丢帧现象，也就是界面会开始变卡。。。

Android异步执行任务的方法有以下几种：

# AsyncTask
AsyncTask是最常用的异步方法，功能结构设计的也很丰富，给使用者足够的控制，使用上主要是将异步执行的任务放在下面方法里。

```
protected Result doInBackground(Params... params)
```
然后调用`.execute(params)`方法即可。

AsyncTask的执行逻辑在API Level 3只能串行执行， 在API Level 4改成了最多128个线程的线程池执行，API Level 11则改成了缺省所有的AsyncTask是在一个线程中顺序执行的，这样可以保证执行和提交的次序一致，如果希望能并发的执行，可以用下面的方法在线程池内执行：

```
task.executeOnExecutor(AsyncTask.THREAD_POOL_EXECUTOR, params);
```

`AsyncTask.THREAD_POOL_EXECUTOR`是`ThreadPoolExecutor`的一个实例，配置是最少5个线程，最多128个。如果需要自己来配置线程池大小，你可以传递自己配置的一个实例到上述方法。

使用AsyncTask需要注意的几个问题：

## 碎片化问题
因为不同版本的Android AsyncTask缺省执行逻辑并不一样，可能在不同机型上表现不一致。如果要自己控制AsyncTask的并发度，解决这个问题的建议是复制Android SDK的AsyncTask源码自己实现一个AsyncTask。

## Activity生命周期问题
Activity可能早于AsyncTask执行完被销毁，如果AsyncTask还继续执行，有可能会浪费资源，并且如果AsyncTask里引用了Activity或部分的View Hierarchy，还会造成引用的对象不会被垃圾回收而引起内存泄漏。通常AsyncTask会定义为Activity的一个匿名inner class，这会建立一个隐式的引用到Activity。

解决的方法是：

1. 在Activity里的`onPause`方法里及时取消不需要再执行的AsyncTask（这种方法在切换到横屏时会重启异步任务，有点浪费）
2. 更好的是使用retained headless fragment来解决生命周期问题，具体演示代码见参考链接

Async比较合适的是较短的（1，2秒），CPU密集计算或读写文件等阻塞IO操作。耗时较长的网络调用用Async不是最合适的。

# Handler & HandlerThread
Handler的异步编程是基于消息队列模型的。执行任务的线程称之为Looper线程，其他线程则将需要异步执行的任务发送给Looper线程--插入其消息队列，方法有：post（较方便使用，但每次需要创建新对象）或sendMessage（较高效，复用消息实例，适合执行大量类似的异步任务）

## Looper
Looper和它的名字意思一样就是Looper线程会永远循环，当没有消息的时候，Looper线程（消费者）会使用(`Object.wait`)方法等待其他线程（生产者）插入新的任务消息，这时候其他线程(`Object.notify`)

Android的主线程其实就是一个Looper线程。

需要注意的是，使用Handler和AsyncTask一样，要注意匿名inner class对Activity的隐式引用而造成内存泄漏，所以使用的时候要记得清理；

解决方法是使用对使用的Activity中的View对象用Weak Reference，并处理当View对象为null的情况。

Handler适合更长一点的（>2秒）的异步任务处理。

# Loader
Loader在Android编程框架中被广泛用于后台加载数据（从文件，数据库甚至网络）。

## AsyncTaskLoader
具体的Loader实现。

## CursorLoader
用户数据库数据后台加载。

Loader在使用上比较大的优势是和Activity的部分解耦，更见到的生命周期管理。

# IntentService
IntentService是Service的一个实现类。其内部实现包含了一个HandlerThread，当任务提交给IntentService时，会被加到队列并顺序处理。

```
public class MyIntentService extends IntentService {
     public MyIntentService() {
       super("thread-name");
     }
     protected void onHandleIntent(Intent intent) {
       // executes on the background HandlerThread.
     } 
}

Intent intent = new Intent(context, MyIntentService.class);
intent.setData(uri); intent.putExtra("param", "some value"); 
startService(intent);
```

IntentService返回任务结果给Activity可以有下面几种方法（Service和Activity的通信方法）：

1. Activity启动IntentService时候，传递一个PendingIntent对象给IntentService，IntentService在处理完任何后调用`PendingIntent.send()`通知Activity，Activity在`onActivityResult`方法内处理收到的消息。
2. 提交一个系统通知，这样App不在前台，用户也可以知道任务完成，比如下载地图数据。
3. 使用Messenger发送一个消息给Activity里的Handler。
4. 使用广播方式把结果封装在Intent中广播出去， 这样Fragment或Activity都可以通过监听广播获得处理结果。

IntentService可以完全和Activity解耦。适合即使App主界面退出情况下也必须完成的任务，比如后台上传日志，图片之类的任务。

# Service
Service合适执行独立于任何Activity或Fragment的任务，即使App主界面退出情况下也必须完成的任务；或者需要比IntentService更高或更细致的并发控制。

# AlarmManager
AlarmManager可以不在人工干预下定时执行任务，比如定时检查邮件，下载更新，或同步内容到云端。

# 参考链接
1. [Asynchronous Android](http://www.packtpub.com/concurrent-programming-on-android/book)
2. [Android内核](http://baike.baidu.com/link?url=syxjYQCIl0gmUPQJGs-oBGffNzRcKNqX4jow2gmUsPhcEH7mSu9dXBijpVglpMBs1iTcUv9Pwm5NEFP_KCSWIq)
3. [^3][帧率与刷新率的区别](http://zhidao.baidu.com/link?url=axWjZPYDt3aRgA4EhoNwicCR5j9hMcRscCNZaVThX-JnbRIcPAH6a_t_BR5lgV9fC8CXQeRLogFLZjLvvy8G_a)