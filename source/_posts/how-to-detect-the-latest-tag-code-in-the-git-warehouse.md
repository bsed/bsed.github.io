---
title: 如何检出git仓中最新的tag的代码
date: 2014-01-08 10:34:00
updated: 2017-01-07 10:35:05
tags: 
- jquery
categories: 
- java

---
```
# Get new tags from remote  
git fetch --tags  
  
# Get latest tag name  
latesttag=$(git describe --tags `git rev-list --tags --max-count=1`)  
  
# Checkout latest tag  
git checkout $latesttag 
```