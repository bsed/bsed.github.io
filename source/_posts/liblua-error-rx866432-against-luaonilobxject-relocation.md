---
title: Liblua error: relocation R_X86_64_32 against `luaO_nilobject_’
date: 2015-10-20 23:39:00
updated: 2017-02-24 23:40:45
tags: 
- ubuntu
- qt
categories: 
- linux

---
The full error will be like as below:
————————
/usr/bin/ld: /usr/local/lib/liblua.a(lapi.o): relocation R_X86_64_32 against `luaO_nilobject_’ can not be used when making a shared object; recompile with -fPIC
/usr/local/lib/liblua.a: could not read symbols: Bad value
————————
I got the above error in between easyapache. It’s pretty sure lua is present in server but something went unoticed or bad while its installation. The missing part was nothing but I compiled 32 bit lua in 64 bit server, so the above error was showing mismatch class error for liblua.
I tried to recompile lua as follows:
Download lua source archive from their official site http://www.lua.org/download.html


<!--more-->


# cd /usr/src
```
# curl -R -O http://www.lua.org/ftp/lua-5.2.3.tar.gz
```
# tar zxf lua-5.2.3.tar.gz
Now you need to make sure that you will compile this time for 64 bit class.
For compilation in 64 bit arch server, you should make the following changes in lua source file before proceeding with installation.
# cd lua-5.2.3

# vi src/Makefile
Change
```bash
CFLAGS= -O2 -Wall -DLUA_COMPAT_ALL $(SYSCFLAGS) $(MYCFLAGS)
```bash
to
```
CFLAGS= -O2 -Wall -DLUA_COMPAT_ALL $(SYSCFLAGS) -fPIC $(MYCFLAGS)
```
Save and exit from file.
Now do the installlation.
```
# make linux test
# make
# make install
```
you are done  now the job left is to finish the easyapache process.
