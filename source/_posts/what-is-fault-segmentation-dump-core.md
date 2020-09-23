---
title: 什么是Segmentation fault(Core Dump)? + 我遇到的实例问题
date: 2015-09-03 21:20:00
updated: 2016-06-02 11:08:18
tags: 
- centos
- nfs
categories: 
- linux

---
转：http://blog.chinaunix.net/space.php?uid=21830881&do=blog&cuid=2093542

什么是Core Dump?
Core的意思是内存, Dump的意思是扔出来, 堆出来.
开发和使用Unix程序时, 有时程序莫名其妙的down了, 却没有任何的提示(有时候会提示core dumped). 这时候可以查看一下有没有形如core.进程号的文件生成, 这个文件便是操作系统把程序down掉时的内存内容扔出来生成的, 它可以做为调试程序的参考.
core dump又叫核心转储, 当程序运行过程中发生异常, 程序异常退出时, 由操作系统把程序当前的内存状况存储在一个core文件中, 叫core dump.

如何使用core文件?
gdb -c core文件路径 [应用程序的路径]
进去后输入where回车, 就可以显示程序在哪一行down掉的, 在哪个函数中.


<!--more-->


为什么没有core文件生成呢?
有时候程序down了, 但是core文件却没有生成. core文件的生成跟你当前系统的环境设置有关系, 可以用下面的语句设置一下, 然后再运行程序便成生成core文件.
ulimit -c unlimited
【没有找到core文件，我们改改ulimit的设置，让它产生。1024是随便取的，要是core文件大于1024个块，就产生不出来了。）
$ ulimit -c 1024 （转者注: 使用-c unlimited不限制core文件大小】

core文件生成的位置一般于运行程序的路径相同, 文件名一般为core.进程号

4. 用gdb查看core文件:
下面我们可以在发生运行时信号引起的错误时发生core dump了.
发生core dump之后, 用gdb进行查看core文件的内容, 以定位文件中引发core dump的行.
gdb [exec file] [core file]
如:
gdb ./test test.core
在进入gdb后, 用bt命令查看backtrace以检查发生程序运行到哪里, 来定位core dump的文件->行.

===========================================================================

造成程序core dump的原因很多，这里根据以往的经验总结一下：

1 内存访问越界

  a) 由于使用错误的下标，导致数组访问越界

  b) 搜索字符串时，依靠字符串结束符来判断字符串是否结束，但是字符串没有正常的使用结束符

  c) 使用strcpy, strcat, sprintf, strcmp, strcasecmp等字符串操作函数，将目标字符串读/写爆。应该使用strncpy, strlcpy, strncat, strlcat, snprintf, strncmp, strncasecmp等函数防止读写越界。

 

2 多线程程序使用了线程不安全的函数。

应该使用下面这些可重入的函数，尤其注意红色标示出来的函数，它们很容易被用错：

asctime_r(3c) gethostbyname_r(3n) getservbyname_r(3n) ctermid_r(3s) gethostent_r(3n) getservbyport_r(3n) ctime_r(3c) getlogin_r(3c) getservent_r(3n) fgetgrent_r(3c) getnetbyaddr_r(3n) getspent_r(3c) fgetpwent_r(3c) getnetbyname_r(3n) getspnam_r(3c) fgetspent_r(3c) getnetent_r(3n) gmtime_r(3c) gamma_r(3m) getnetgrent_r(3n) lgamma_r(3m) getauclassent_r(3) getprotobyname_r(3n) localtime_r(3c) getauclassnam_r(3) etprotobynumber_r(3n) nis_sperror_r(3n) getauevent_r(3) getprotoent_r(3n) rand_r(3c) getauevnam_r(3) getpwent_r(3c) readdir_r(3c) getauevnum_r(3) getpwnam_r(3c) strtok_r(3c) getgrent_r(3c) getpwuid_r(3c) tmpnam_r(3s) getgrgid_r(3c) getrpcbyname_r(3n) ttyname_r(3c) getgrnam_r(3c) getrpcbynumber_r(3n) gethostbyaddr_r(3n) getrpcent_r(3n)

 

3 多线程读写的数据未加锁保护。

对于会被多个线程同时访问的全局数据，应该注意加锁保护，否则很容易造成core dump

 

4 非法指针

  a) 使用空指针

  b) 随意使用指针转换。一个指向一段内存的指针，除非确定这段内存原先就分配为某种结构或类型，或者这种结构或类型的数组，否则不要将它转换为这种结构或类型的指针，而应该将这段内存拷贝到一个这种结构或类型中，再访问这个结构或类型。这是因为如果这段内存的开始地址不是按照这种结构或类型对齐的，那么访问它时就很容易因为bus error而core dump.

 

5 堆栈溢出
不要使用大的局部变量（因为局部变量都分配在栈上），这样容易造成堆栈溢出，破坏系统的栈和堆结构，导致出现莫名其妙的错误。

============================================================================

我发生的问题（待解决中）：

    main.h:
    typedef struct TS_PAT
    {
      UINT8 table_id;
      UINT32 zero;
    } TS_PAT;
    
    main.c:
    TS_PAT *PAT_DataTable;
    
    void Load_PAT_table(FILE *stream){
        PAT_DataTable->table_id = 1;
    }

上记代码，编译通过。


执行时:
Segmentation fault(core dumped)


用上记方法，最后的bt命令输入后：

    #0  0x080486e7 in Load_PAT_table (stream=0x804c050) at main.c:76
    76              PAT_DataTable->table_id = 1;
    (gdb) bt
    #0  0x080486e7 in Load_PAT_table (stream=0x804c050) at main.c:76
    #1  0x080487a5 in Parse_Adjust_PAT_table (stream=0x804c050) at main.c:137
    #2  0x08049651 in main (argn=3, argc=0xbf96e284) at main.c:827

当注释掉”PAT_DataTable->table_id = 1;“的时候，没问题。
否则，在这行赋值语句执行时就down机。

原因：查明，，未对指针初始化（未分配起指向的内存空间）
http://topic.csdn.net/u/20091113/14/da645a0f-8c92-449d-b29d-f6c83561728c.html
