---
title: Gperftools中tcmalloc的简介和使用
date: 2014-12-29 18:11:00
updated: 2015-01-03 13:18:33
tags: 
- css
- 自动适配
categories: 
- css

---
TcMalloc（Thread-CachingMalloc）是google-perftools工具中的一个内存管理库，与标准的glibc库中malloc相比，TcMalloc在内存分配的效率和速度上要高很多，可以提升高并发情况下的性能，降低系统的负载。
Google-perftools项目的网址为：http://code.google.com/p/google-perftools/，该项目包括TcMalloc、heap-checker、heap-profiler和cpu-profiler共4个组件。在只使用TcMalloc情况下可以不编译其他三个组件。


<!--more-->


注：使用线程内存池的方法，在小对象是在内存池中进行分配，使用分配较多的内存空间来优化分配时间。
实现原理请参考网址http://goog-perftools.sourceforge.net/doc/tcmalloc.html。
简介
TcMalloc是一个由Google开发的，比glibc的malloc更快的内存管理库。通常情况下ptmalloc2能在300ns执行一个malloc和free对，而TcMalloc能在50ns内执行一个malloc和free对。
TcMalloc可以减少多线程程序之间的锁争用问题，在小对象上能达到零争用。
TcMalloc为每个线程单独分配一个线程本地的Cache，少量的地址分配就直接从Cache中分配，并且定期做垃圾回收，将线程本地Cache中的空闲内存返回给全局控制堆。
TcMalloc认为小于（<=）32K为小对象，大对象直接从全局控制堆上以页（4K）为单位进行分配，也就是说大对象总是页对齐的。
TcMalloc中一个页可以存入一些相同大小的小对象，小对象从本地内存链表中分配，大对象从中心内存堆中分配。
安装
Linux下tcmalloc的安装过程如下：
1）  从Google源代码网址上下载源代码包，现在最新版本为2.0；
2）  解压缩源代码包
    unzip gperftools-2.0.zip或
     tar zxvf gperftools-2.0.tar.gz

3）  编译动态库
 

    cd gperftools-2.0
     ./ configure  --disable-cpu-profiler --disable-heap-profiler--disable-heap-checker 
    --disable-debugalloc--enable-minimal

加入上面的参数是为了只生成tcmalloc_minimal动态库，如果需要所有组件，命令如下：

    # ./configure
    # ./configure -h           用于查看编译选项。

编译和安装：

    # make&& make install

使用最小安装时把tcmalloc_minimal的动态库拷贝到系统目录中：

    # cplib/tcmalloc_minimal.so.0.0.0 /usr/local/lib

创建软连接指向tcmalloc：

    # ls –s /usr/local/lib/libtcmalloc_minimal.so.0.0.0/usr/local/lib/libtcmalloc.so

启动程序之前，预先加载tcmalloc动态库的环境变量设置：

    # exportLD_PRELOAD=”/usr/local/lib/libtcmalloc.so

使用losf检查程序是否已经加载tcmalloc库：

    # lsof -n | greptcmalloc

在Linux下使用的tcmalloc安装完成，在Windows下使用VS（2003以上版本）打开工程项目gperftools.sln进行编译。
使用
将libtcmalloc.so/libtcmalloc.a链接到程序中，或者设置LD_PRELOAD=libtcmalloc.so。这样就可以使用tcmalloc库中的函数替换掉操作系统的malloc、free、realloc、strdup内存管理函数。可以设置环境变量设置如下：
         TCMALLOC_DEBUG=<level>       调试级别，取值为1-2
         MALLOCSTATS=<level>                    设置显示内存使用状态级别，取值为1-2
         HEAPPROFILE=<pre>                     指定内存泄露检查的数据导出文件
         HEAPCHECK=<type>                        堆检查类型，type=normal/strict/draconian
TcMalloc库还可以进行内存泄露的检查，使用这个功能有两种方法：
1）将tcmalloc库链接到程序中，注意应该将tcmalloc库最后链接到程序中；
2）设置LD_PRELOAD=”libtcmalloc.so”/HEAPCHECK=normal，这样就不需重新编译程序
打开检查功能，有两种方式可以开启泄露检查功能：
1）  使用环境变量，对整个程序进行检查： HEAPCHECK=normal /bin/ls
2）  在源代码中插入检查点，这样可以控制只检查程序的某些部分，代码如下：
HeapProfileLeakCheckerchecker("foo");        // 开始检查
Foo();                                                                         // 需要检查的部分
assert(checker.NoLeaks());                                 // 结束检查
调用checker建立一个内存快照，在调用checker.NoLeaks建立另一个快照，然后进行比较，如果内存有增长或者任意变化，NoLeaks函数返回false，并输出一个信息告诉你如何使用pprof工具来分析具体的内存泄露。
    执行内存检查:
         #LD_PRELOAD=libtcmalloc.so HEAPCHECK=strict HEAPPROFILE=memtm ./a.out
执行完成后会输出检查的结果，如果有泄露，pprof会输出泄露多少个字节，有多少次分配，也会输出详细的列表指出在什么地方分配和分配多少次。
         比较两个快照：
         #pprof --base=profile.0001.heap 程序名 profile.0002.heap
    已知内存泄漏时，关闭内存泄露检查的代码：
    void *mark =HeapLeakChecker::GetDisableChecksStart();
    <leaky code>           //不做泄漏检查的部分
    HeapLeakChecker::DisableChecksToHereFrom(mark);
         注：在某些libc中程序可能要关闭检查才能正常工作。
         注：不能检查数组删除的内存泄露，比如：char *str = new char[100]; delete str;。