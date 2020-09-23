---
title: Linux之磁盘管理工具ncdu
date: 2014-12-25 11:30:47
updated: 2014-12-25 11:38:56
tags: 
- php
- foreach
categories: 
- php

---
 最近线上设备经常/目录遇到DF的情况，由于我们设备磁盘在/下有data目录下有cache[1-12]等多个挂载点，直接du –sh /的话会卡死很久很久。所以每次都得挨个目录的去查，很是郁闷。
![image_thumb2.png][1]
想起来图形下的Disk Usage Analyser 会自动分析每个路径下的各位目录、文件的大小，于是想找一个替代品，终于发现了ncdu这货。先秀下最终效果，好不好看疗效嘛：


<!--more-->


 可以看出来/下占用磁盘空间最大的目录为data目录，进入data目录下：
![image_thumb4.png][2]
 发现proclog、backup、refresh_db占用了较多的磁盘空间。同时可以看到红线框中、带有<的目录是跳过不分析的。经常ncdu的分析就很容易看出来哪些东西占用了大量空间了吧，比du –sh 好用多了。
下面说下具体的安装、使用：
1.介绍：

          Not quite happy with the available disk usage analyzers and looking for a fun project to get used to C programming, I started working on ncdu: A disk usage analyzer with an ncurses interface, aimed to be run on a remote server where you don’t have an entire gaphical setup, but have to do with a simple SSH connection. ncdu aims to be fast, simple and easy to use, and should be able to run in any minimal POSIX-like environment with ncurses installed.
   
          英语你们都懂得，大致意思就是说作者不爽现有的磁盘分析工具，于是就自己写了一个。ncdu是基于ncurses 库的，所以可以在终端下使用，至于有点就是速度快、输出美观，另外还有很多的交互式命令。

2.安装：   

         安装和别的软件安装没什么太大区别，我这边是centos5.5 x64的环境，直接安装个epel源，然后yum -y install ncdu就ok了。
 
          这部分详见官网吧：http://dev.yorhel.nl/ncdu
3.使用方法：
         a)使用帮助：
![image_thumb5.png][3]
        首先使用ncdu –h  查看下用法，很简单，没特别要说的，唯一的一个比较赞的功能就是–exclude 选项，可以跳过你不想分析的目录。
        b)使用：     
      直接执行ncdu即可，当然默认不指定目录的话会分析当前目录的磁盘使用情况。
![image_thumb6.png][4]
上下箭头即可移动，同时回车可以进入选择的目录进行操作。
需要留心的就是输入?就调出来一个菜单，里面有很多的帮助信息，内容如下：
![image_thumb7.png][5]
  n ：按文件名进行排序 
                s ：按文件大小进行排序
                d ：删除文件或者目录
                t  ：排序时始终跟随当前选择项 
                g ：用#的形式显示显示各文件/目录的大小所占的百分比
                 i ：显示选择目录或者文件的详细信息 
                 r ：重新统计当前文件夹大小
                q ：退出ncdu 
   
               大致就这些命令了
   
             试下g命令的效果，如下图：

        ![image_thumb8.png][6]

    i命令的效果，如下图：
![image_thumb9.png][7]

    d命令的效果，如下图：     
![image_thumb10.png][8]
4.实例：          
      说了那么多，如果实践中用不上，再牛的工具也只是耍流氓嘛。所以我们来试着统计下/目下的磁盘使用情况。想直接du –sh / 的同学可以洗洗睡了。
      前面说过由于我们/下存在多个挂载点，所以分析的时候要跳过这些，不然会等很久，而且这些目录是挂载点、挂载了有其他的磁盘，统计的大小也和/目录的磁盘使用情况无关。
`[root@CXN-XX-1-3X4 /]# ncdu --exclude cache* /`
  如上简单的一行命令就可以搞定了。–exclude 跳过了匹配cache关键字的目录或者文件。
![image_thumb11.png][9]
  可以看到红框的部分就跳过了未分析。同时可以看到proclog目录占用了大量磁盘空间，不过这个是我们存放的业务日志。返回到/：
![image_thumb13.png][10]
使用s命令排序之后发现，/usr目录占用了8G 的空间，依次进入/usr/local/squid目录：
![image_thumb14.png][11]
发现bin目录下占用了2.8G ,很明显是有问题的，再次进入bin下级各个目录：
![image_thumb15.png][12]
终于发现error.log这个隐藏非常深的日志未分割，占用了2.8G 的空间，d命令删除即可。

5.总结：
     ncdu还是非常犀利的，感觉我可以扔掉du命令了，一个ncdu完全可以搞定以后所有的DF告警了。ncdu分析的同时还可以很方面的管理各个目录、查询详细信息、甚至直接删除目录文件等

  [1]: https://imgs.gnux.cn/usr/uploads/2014/12/1005472086.png
  [2]: https://imgs.gnux.cn/usr/uploads/2014/12/4176254197.png
  [3]: https://imgs.gnux.cn/usr/uploads/2014/12/3046083748.png
  [4]: https://imgs.gnux.cn/usr/uploads/2014/12/17282749.png
  [5]: https://imgs.gnux.cn/usr/uploads/2014/12/4256654103.png
  [6]: https://imgs.gnux.cn/usr/uploads/2014/12/1271978750.png
  [7]: https://imgs.gnux.cn/usr/uploads/2014/12/921027948.png
  [8]: https://imgs.gnux.cn/usr/uploads/2014/12/2655697671.png
  [9]: https://imgs.gnux.cn/usr/uploads/2014/12/1156679334.png
  [10]: https://imgs.gnux.cn/usr/uploads/2014/12/889219004.png
  [11]: https://imgs.gnux.cn/usr/uploads/2014/12/1258907761.png
  [12]: https://imgs.gnux.cn/usr/uploads/2014/12/2290436012.png