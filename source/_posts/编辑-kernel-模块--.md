---
title: "编辑 kernel 模块  "
categories: [ "Linux" ]
tags: [ "linux","kernel" ]
draft: false
slug: "linux-kernel-make"
date: "2011-11-15 14:04:00"
---

    make oldconfig
    
    make prepare
    
    make scripts
    
    make -C /usr/src/linux M=`pwd` modules