---
title: 解决：/usr/bin/ld: /usr/local/lib/liblua.a(lutf8lib.o): relocation R_X86_64_32S against
date: 2017-01-13 23:24:00
updated: 2017-02-24 23:28:33
tags: 
- golang
- rsa
categories: 
- go

---
错误为:
```
/usr/bin/ld: /usr/local/lib/liblua.a(lapi.o): relocation R_X86_64_32 against `luaO_nilobject_' can not be used when making a shared object; recompile with -fPIC  
/usr/local/lib/liblua.a: could not read symbols: 错误的值  
```
原因:前几天安装了lua5.3版本(make linux,make install安装)
今天去make一个依赖于lua库的项目时候就出现了这个问题
解决方法:我也没好招, 去lua官网下载Lua 5.1 解压后修改src目录下的Makefile:CFLAGS=加上-fPIC
然后再编译安装
make linux
make isntall 
解决
