---
title: "Ubuntu Linux 下文件名乱码（无效的编码）的快速解决办法"
categories: [ "Linux" ]
tags: [ "linux","convmv" ]
draft: false
slug: "ubuntu-linux-under-the-file-name-garbled-invalid-encoding-fast-solution"
date: "2011-09-03 13:57:00"
---

文件是在WIndows 下创建的,Windows 的文件名中文编码默认为GBK,而Linux中默认文件名编码为UTF8,由于编码

不一致所以导致了文件名乱码的问题，解决这个问题需要对文件名进行转码。文件名转码工具convmv没安装的话

用下面的命令安装：

`sudo apt-get install convmv`
复制代码

convmv 使用方法：

convmv -f 源编码 -t 新编码 [选项] 文件名

常用参数：


<!--more-->


-r 递归处理子文件夹

–notest 真正进行操作，默认情况下是不对文件进行真实操作

–list 显示所有支持的编码

–unescap 可以做一下转义，比如把%20变成空格

应用举例：（看最后几段就是批量转化的结果）

正在读取软件包列表... 完成
正在分析软件包的依赖关系树       
正在读取状态信息... 完成       
下列【新】软件包将被安装：
  convmv
升级了 0 个软件包，新安装了 1 个软件包，要卸载 0 个软件包，有 0 个软件包未被升级。
需要下载 19.9 kB 的软件包。
解压缩后会消耗掉 78.8 kB 的额外空间。
获取：1 http://mirrors.aliyun.com/ubuntu/ vivid/universe convmv all 1.15-2 [19.9 kB]
下载 19.9 kB，耗时 0秒 (143 kB/s)
正在选中未选择的软件包 convmv。
(正在读取数据库 ... 系统当前共安装有 497085 个文件和目录。)
正准备解包 .../archives/convmv_1.15-2_all.deb  ...
正在解包 convmv (1.15-2) ...
正在处理用于 man-db (2.7.0.2-5) 的触发器 ...
正在设置 convmv (1.15-2) ...

root@gnux:~/文档/pdf/3# ls
╡┌┴∙╒┬  ╡┌╦─╒┬  ╡┌╥╗╒┬  ╡┌╢■╒┬  HTML5TEST  ╡┌╩о╥╗╒┬  ╡┌╬х╒┬
╡┌╛┼╒┬  ╡┌╚¤╒┬  ╡┌╞▀╒┬  ╡┌░╦╒┬  ╡┌╩о╒┬     ╡┌╩о╢■╒┬

主要方法:convmv -f GBK -t UTF-8 --notest -r *[把当前文件夹下所有乱码文件名改过来]