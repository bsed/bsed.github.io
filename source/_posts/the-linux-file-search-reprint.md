---
title: 简述Linux文件搜索(转载)
date: 2012-10-23 14:16:00
updated: 2015-01-23 14:20:35
tags: 
- mingw
- pkg-config
categories: 
- linux

---
## 1 、关于搜索；##

在Linux文件系统中，搜索概念有两种，一种是搜索文件名，另一种是在一个文件中搜索指定的内容；这两种搜索，我们都简要的介绍一下，但不一定极为专业。想到哪写到哪；


## 2、通过搜索文件名来查找文件；##

有的弟兄经常会说文件都放在哪里了，我怎么查找文件？其实这里说的意思就是通过文件名来查找文件所处的位置；搜索文件名，我们常用的工具有find、locate、where等。现在我们简单的说一说这几个工具在文件系统中，通过查找文件名的方法来查看其所在的位置；


<!--more-->


### 2.1 find ；###


find  路径    -name  文件名
 

举例：比如在/etc中搜索vsftpd.conf文件；

 

[root@localhost ~]# find     /etc  -name vsftpd.conf
find 更为详细的说明请参考： 《Linux文件查找命令find,xargs详述》 

find 是最强劲的搜索工具，用法也比较灵活，而不是仅仅找一个文件名这个功能。


### 2.2 locate ###

locate 是用来通过文件名搜索文件的工具，它的用法也比较简单。locate 的应用，首先要通过updatedb建立索引数据库，然后才能应用；如果您新安装了软件或者存放了新的文件，也要先运行updatedb命令，以生成最新索引库。

用法：

 

locate 文件名
 

我们要先运行updatedb；


`[root@localhost ~]# updatedb`
 

举例： 我们要找vsftpd.conf文件都位于哪个位置；

    [root@localhost ~]# locate vsftpd.conf
    /etc/vsftpd/vsftpd.conf
    /home/beinan/vsftpd.conf
    /root/.vsftpd.conf.swp
    /root/vsftpd.conf
    /root/vsftpd.config
    /usr/share/doc/vsftpd-2.0.4/EXAMPLE/INTERNET_SITE/vsftpd.conf
    /usr/share/doc/vsftpd-2.0.4/EXAMPLE/INTERNET_SITE_NOINETD/vsftpd.conf
    /usr/share/doc/vsftpd-2.0.4/EXAMPLE/VIRTUAL_USERS/vsftpd.conf
    /usr/share/logwatch/default.conf/logfiles/vsftpd.conf
    /usr/share/logwatch/default.conf/services/vsftpd.conf
    /usr/share/man/man5/vsftpd.conf.5.gz

 


### 2.3 whereis 和which ###


#### 2.3.1 where ####

whereis 是来寻找命令的二进制文件，同时也会找到其帮助文件；

比如我们不知道fdisk工具放在哪里，我们就可以用whereis fdisk 来查找；
 

    [root@localhost ~]# whereis fdisk
    fdisk: /sbin/fdisk /usr/share/man/man8/fdisk.8.gz

 

如果我们寻找grub在哪里，应该用如下命令，说明grub位于/sbin目录，他的帮助文件是grub.8.gz ，也就是man grub；

 

[root@localhost ~]# whereis grub
grub: /sbin/grub /etc/grub.conf /usr/share/grub /usr/share/man/man8/grub.8.gz
 


#### 2.3.2 which ####

which 和where 相似，只是我们所设置的环境变量中设置好的路径中寻找；比如；

    [root@localhost ~]# which fdisk
    /sbin/fdisk


## 3、在一个文件或输出中查找；##

有时我们为了管理服务器，可能要查看一些日志文件或管理指令的输出，并抽取出来；这时我们要用到more和grep、egrep、|（管道），如果要输出到一个文件中， 还要用到 > 。

比如我们查看/var/log/message 文件，并查找5月8号的日志；我们应该用如下的命令组合；

    [root@localhost ~]# cat  /var/log/messages |grep 'May  8' |more

注意：因为May和8之间有两个空格，所以得用”号括起来。

如果我们想把查看到的结果输出到一个文件中，应该用 > 输出到文件；

 
    [root@localhost ~]# cat  /var/log/messages |grep 'May  8' > ~/message0508.txt
    [root@localhost ~]# more ~/message0508.txt


上面的例子，是我们首先用 cat 来查看/var/log/message 的内容，然后抽取带有 May 8字样的行，然后输出到用户家目录下的 message0508.txt文件中，然后再用more来查看message0508.txt文件内容；

从一个输出结果中查找

我们也可以从一个输出的结果中查找所需要的内容，请看下面的例子；
 

    [root@localhost ~]# ls -lh
    总计 24M
    -rwxr-xr-x 1 root root  545 04-25 11:21 adduml02.sh
    -rwxr-xr-x 1 root root  545 2004-01-18 adduml.sh
    -rw-rw-rw- 1 root root    0 04-25 14:26 dood
    drwxr-xr-t 2 root root 4.0K 04-24 21:59 googledir
    -rwxr-xr-x 1 root root    7 04-21 12:47 lsfile.sh
    -rw-r--r-- 1 root root  31K 05-08 13:47 message0508.txt
    drwxr-xr-x 2 root root 4.0K 04-21 12:46 mkuml-2004.07.17
    -rwxr-xr-x 1 root root  67K 04-22 14:13 mkuml-2004.07.17-ananas.tar.bz2
    drwxr-xr-x 2 root  502 4.0K 04-25 09:08 mydir
    -rw-r--r-- 1 root root 7.9M 04-27 20:35 myfile.img
    -rw-r--r-- 1 root root 4.0M 04-27 20:37 myfileSpaa
    -rw-r--r-- 1 root root 3.9M 04-27 20:37 myfileSpab
    -rw-r--r-- 1 root root 7.9M 04-27 20:38 newmyfile.img
    drwxrw-rw- 2 root root 4.0K 04-25 14:22 sundir
    drwxr-xr-x 2 root root 4.0K 04-25 09:20 testdir
    -rwxr-xr-x 1 root root  613 03-26 18:41 upgrade.log
    -rw------- 1 root root 4.0K 04-29 20:12 vsftpd.conf
    -rw-r--r-- 1 root root 4.0K 04-29 20:23 vsftpd.config
    -rw-r--r-- 1 root root  100 04-27 16:14 xaa
     

在ls -lh 的输出结果中，查看带有04-27字样的行，我们看到带有04-27字样的行都出来了。04-27在这里表示文件创建或最后修改（访问）的时间。符合这一特征的都在里面。


    [root@localhost ~]# ls -lh |grep '04-27' |more
    -rw-r--r-- 1 root root 7.9M 04-27 20:35 myfile.img
    -rw-r--r-- 1 root root 4.0M 04-27 20:37 myfileSpaa
    -rw-r--r-- 1 root root 3.9M 04-27 20:37 myfileSpab
    -rw-r--r-- 1 root root 7.9M 04-27 20:38 newmyfile.img
    -rw-r--r-- 1 root root  100 04-27 16:14 xaa

 

查找正在运行的程序：

这也是从一个输出中查找的例子，先用ps 来列出所有正在运行中的进程，然后通过grep 来提取。下面的例子中查找是否有gaim程序在运行；


    [root@localhost ~]# ps -aux |grep gaim
    Warning: bad syntax, perhaps a bogus '-'? See /usr/share/doc/procps-3.2.6/FAQ
    beinan    2682  0.0  4.0 152644 30188 ?        S    08:59   0:16 gaim
    root      5660  0.0  0.0   5160   720 pts/1    S+   13:58   0:00 grep gaim

 

从上面的例子，我们可以看到的确有gaim运行，进程号是2682 。如果想杀掉gaim怎么办？应该有kill 2682 或killall gaim

 

    [root@localhost ~]# kill 2682
    或
    [root@localhost ~]# killall gaim

 

对于进程的查找，也可以用pgrep 来进行；比如我们查找gaim；

 

    [root@localhost ~]# pgrep gaim
    2682

 

等价于；
 

    [root@localhost ~]# ps -aux |grep gaim
    Warning: bad syntax, perhaps a bogus '-'? See /usr/share/doc/procps-3.2.6/FAQ
    beinan    2682  4.4  2.3 105000 17504 ?        S    14:05   0:02 gaim
    root      5716  0.0  0.0   5156   712 pts/1    R+   14:06   0:00 grep gaim
     


## 4、关于本文；##

关于查找的命令和工具说起来比较复杂，本文也仅仅是一个入门性的文档。如果只是通过文件名来查找到他位置，我感觉还是用locate好一点。有时find太费时间 ；

这篇文章本来仅仅是写文件和目录的搜索的，后来我想到：可能有的弟兄要学一点指定关健字在一个文件或输出中查找。所以又写了一点在一个文件或输出中查找所需要的内容。看上去本文有点拼凑的感觉，是不是太随意了？