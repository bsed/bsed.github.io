---
title: LNMP之php内存+nginx之CPU优化
date: 2016-07-05 21:43:00
updated: 2016-11-21 21:45:11
tags: 
- mvn
categories: 
- linux

---
## 一、如果你使用的是lnmp默认的安装的php5.2.17sp1版本
```
/usr/local/php/etc/php-fpm.conf #优化的文件
```
找到`<value name="max_children">`，按你的内存容量修改PHP进程的数量，一般128内存开启6个，256内存开启16个。这样能保证，在PHP大并发的情况下不会502错误。

## 二、如果您升级到了5.3.x版本
```
/usr/local/php/etc/php-fpm.conf #优化的文件
pm.max_children = 32 #最多运行数量
pm.start_servers = 16 #开机启动数量
pm.min_spare_servers = 16 #最小运行数量
pm.max_spare_servers = 32 #最大运行数量
```
需要同时修改这些数字，您可以自行定义，参考上述内容即可。


<!--more-->


## 三、Nginx性能优化CPU参数worker_cpu_affinity使用说明

如果2核心：
```
worker_processes     2;
worker_cpu_affinity 0101 1010;
```
如果是4核心：
```
worker_processes     4;
worker_cpu_affinity 0001 0010 0100 1000;
```

