---
title: " Liunx怎么查看zombie进程"
categories: [ "Linux" ]
tags: [ "zombie" ]
draft: false
slug: "liunx-how-to-view-the-zombie-process"
date: "2013-10-25 08:14:00"
---

    ps aux | grep -w Z # 列出整個 pid 的 process 信息
    ps aux | awk '{print $8 " " $2}' | grep -w Z # 列出 Z + pid
    ps aux | awk '$8=="Z" {print $2}' # 只抓出 pid