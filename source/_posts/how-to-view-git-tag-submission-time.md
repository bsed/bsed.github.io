---
title: 如何查看 git tag 的提交时间
date: 2014-12-16 10:46:00
updated: 2017-01-07 10:48:18
tags: 
- java
- cfx
categories: 
- java

---
如下，首先取到 `tag` 的时间戳，然后转换成我想要的格式：

```bash
tagdate=`git log -1 --format=%ct $TAG`  
date -d @$tagdate +%Y%m%d%H%M%S
```