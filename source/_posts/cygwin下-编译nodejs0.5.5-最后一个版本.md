---
title: "cygwin下 编译nodejs0.5.5 最后一个版本"
categories: [ "Linux" ]
tags: [ "nodejs","cygwin" ]
draft: false
slug: "cygwin-compiled-nodejs055-the-final-version"
date: "2011-02-09 16:05:00"
---

node-v0.5.6之后 便不再支持 cygwin 了

 下载源代码 node-v0.5.5.tar.gz
./configure
make 
make install

完成

出现错误的解决办法
Waf: Leaving directory `/home/bpolidor/src/node/build'
Build failed:  -> task failed (err #2):
        {task: libv8.a SConstruct -> libv8.a}
make: *** [program] Error 1
$ gcc --version
gcc (GCC) 4.3.4 20090804 (release) 1
Seems like this was supposed to be addressed based on change log:
Fix V8 build on Cygwin (Bert Belder)

just changed tag to 0.3.1, and i can build fine. 
- show quoted text -
