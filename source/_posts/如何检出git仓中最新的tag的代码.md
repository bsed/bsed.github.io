---
title: "如何检出git仓中最新的tag的代码"
categories: [ "Linux" ]
tags: [ "git" ]
draft: false
slug: "how-to-detect-the-latest-tag-code-in-the-git-warehouse"
date: "2014-01-08 10:34:00"
---

```
# Get new tags from remote  
git fetch --tags  
  
# Get latest tag name  
latesttag=$(git describe --tags `git rev-list --tags --max-count=1`)  
  
# Checkout latest tag  
git checkout $latesttag 
```