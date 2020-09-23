---
title: 如何根据commit id找到对应节点所属的分支和标签？
date: 2014-07-27 10:39:00
updated: 2017-01-07 10:39:49
tags: 
- java
- jsonup
categories: 
- java

---
```bash
git branch --contains commit  
git tag --contains commit 
```