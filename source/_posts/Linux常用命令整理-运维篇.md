---
title: "Linux常用命令整理-运维篇"
categories: [ "Linux" ]
tags: [ "linux","bash","shell" ]
draft: false
slug: "linux-commonly-used-command-sorting-operation-and-maintenance-section"
date: "2018-08-23 06:05:00"
---

```bash
查看占用最高的前10个进程：ps auxw|head -1;ps auxw|sort -rn -k4|head -10
查看文件夹磁盘占用大小命令：du -h –max-depth=1
重启phpfpm 减少内存占用： kill -USR2 cat /alidata/server/php-7.0/var/run/php-fpm.pid
查找大于500M的文件: find / -type f -size +100M
标准输出和错误输出均定向到回收站: java -jar testapp.jar /dev/null 2>&1
查找某目录下的文件信息: ls -lh $(find /home/wwwlogs/LocalIntegrationTestProxy/ - name *.log)
```
**grep 文本搜索工具**

 - -i 忽略大小写
 - -v 不显示匹配行
 - -c 显示符合条件的行数值

**文本搜索支持正则表达式**
 - cat /etc/passwd | grep root // 显示包含root的所有行
 - cat /etc/passwd | grep -v bash // 显示不包含bansh的所有行
 - grep 'search' ./ -r //查看当前目录下文件中有 search 的行


<!--more-->


**tar 解压缩**
 - -x 解压文件
 - -z 支持giz方式
 - -j 支持bzip2方式
 - -v 显示操作过程
 - -f 制定文件
 - -C 解压到指定目录
 - -W 确认压缩文件的正确性
 - -t 显示压缩文件的内容
```bash
tar zxvf file.tar.gz // 解压当前文件
tar zxvf file.tar.gz -C /dir   //  解压文件到指定目录
tar zxvf file.tar.gz file // 将file文件解压出来
tar jxvf file.tar.gz2  // 解压giz2压缩的文件
tar zcvf file.tar.gz source  // 将source 文件压缩,多个文件用空格隔开
tar jcvf file.tar.gz2 source // 将source文件以giz2方式压缩,多个文件用空格隔开
```

**查看压缩文件内容**

 - tar -ztvf [filename]

**压缩文件**

 - tar -zcvf xx.tar.gz [filename/dir] //多目录文件用空格隔开

**解压文件**

 - tar -zxvf file.tar.gz  // 解压到当前目录
 - tar -zxvf file.tar.gz -C [dir]  //解压到指定的目录下面

**find 查找文件**
格式： find [path] -name searchfile ， 文件名支持正则表达式
```bash
find / -name 'root' // 在根目录下面查找root的文件
find / -perm 777 // 查找权限为777的文件
find /home -user user // 在home下查找属于user用户的文件
find /home -nouser // 查找没有属主用户的文件
find /home -group group // 查找属组为group的文件
find /home -nogroup // 查找没有属组的文件
find /home -type d // 查找类型为目录的文件scp 文件安全传输
// local->remote 格式
scp srcfile remote_user@remote_ip:dest_dir
scp -r srcdir remote_user@remote_ip:dest_dir
// remote->local 格式
scp remote_user@remote_ip:dest_dir /localdir
scp -r remote_user@remote_ip:dest_dir /localdir
```

**tail 查看文件最后10行信息**

 - tail -n num file  // 查看制定行数num
 - tail -f file  // 时时查看写入文件的内容
 - tailf file // 和上面效果一样

**head 查看文件头10行信息**

 - head -n num file  // 查看制定行数num

**cat查看文件全部内容**

 - cat file  // 将文件全部内容打印到终端
 - cat -A file // 查看文件的存储结构,主要是空格和换行符
 - cat -b file  // 显示行号
 - cat /etc/passwd > file // 创建文件
 - cat file1 file2 > file //合并文件

**ps查看进程命令**

 - a 显示所有进程
 - c 显示进程的真实名
 - -A/-e 显示所有进程
 - -au 显示详细进程信息
 - -aux 显示所有包含使用者的进程
 - f 显示进程间的关系
 - -ef 显示全部进程间的关系

```bash
// 常用命令
ps -aux
ps -ef
```
**netstat查看端口命令**

 - -a 显示全部连接的socket
 - -A 列出网络类型
 - -n 直接显示ip地址,不用网络地址
 - -p 显示socket的识别码和程序名称
 - -t 显示tcp传输协议列表
 - -u 显示udp传输协议列表
```bash
// 常用命令
netstat -a
netstat -atnp
netstat -aunp
```
还可以配合 grep 使用
```bash
sort 排序
cat /etc/passwd | sort // 一般都是针对某个结果进行排序
```
**du 查看使用空间**
 - -s 显示总计
 - -h 以K,M,G单位显示信息
 - -m 以MB单位输出
 - -k 以KB单位输出
```bash
du -sh file //查看文件目录总大小
du -c file1 file2 //显示多个文件大小,并显示总和
du -csh dir1 dir2 //显示多个目录总大小,和总和
```
**pwd 查看路径命令**
```bash
pwd  // 显示用户所在当前目录的路径
pwd -P  // 显示真实路径,物理路径
```