---
title: "解决wiznote 缺失libQt5WebEngine.so"
categories: [ "Linux" ]
tags: [ "wiznote" ]
draft: false
slug: "resolve-wiznote-deletion"
date: "2017-05-28 20:46:00"
---

今天逛贴吧，发现一个[问题](https://tieba.baidu.com/p/5121193231)，顺便贴出来解决方法.

错误如下：
```
wjc@wjc-pc:/opt/WizNote$ ./start.sh
/opt/WizNote/lib:/opt/Qt5.8.0/5.8/gcc_64/lib
wjc@wjc-pc:/opt/WizNote$ 
wjc@wjc-pc:/opt/WizNote$ sudo ./start.sh
/opt/WizNote/lib:
./bin/WizNote: error while loading shared libraries: libQt5WebEngine.so.5: cannot open shared object file: No such file or directory
wjc@wjc-pc:/opt/WizNote$ 
```


<!--more-->


这个是由于`libQt5WebEngine.so.5` 文件丢失所致。

我使用的是qt5.7 需要设置下`qt lib` 的环境变量

如下：

```bash
export LD_LIBRARY_PATH=/home/kelvin/Qt5.7/5.7/gcc_64/lib
```

fish环境
```bash
set -x LD_LIBRARY_PATH /home/kelvin/Qt5.7/5.7/gcc_64/lib

```
重新编译 wiznote 源代码即可。

```
cd /home/kelvin/tools/wizteam/WizQTClient/build
make
sudo make install 
```


