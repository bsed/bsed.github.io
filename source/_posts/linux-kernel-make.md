---
title: 编辑 kernel 模块  
date: 2011-11-15 14:04:00
updated: 2014-11-13 14:04:53
tags: 
- golang
- ioutil
categories: 
- go

---
    make oldconfig
    
    make prepare
    
    make scripts
    
    make -C /usr/src/linux M=`pwd` modules