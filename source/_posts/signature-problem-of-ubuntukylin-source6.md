---
title: ubuntukylin 源的签名问题
date: 2015-10-13 22:32:00
updated: 2016-09-30 22:34:35
tags: 
- javascript
- jstips
categories: 
- js

---
我用的ubuntu16.04LTS, 64位。今天使用命令`sudo apt-get update`时，出现以下错误：
```bash
Release
Fetched 3,192 B in 3s (971 B/s)
Reading package lists... Done
W: GPG error: http://archive.ubuntukylin.com:10006/ubuntukylin xenial InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 8D5A09DC9B929006
```


<!--more-->


请问应该如何操作呢？
```bash
sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 8D5A09DC9B929006
```

Executing: /tmp/tmp.grDf6K7ffU/gpg.1.sh --recv-keys
--keyserver
keyserver.ubuntu.com
8D5A09DC9B929006
gpg: key 8D5A09DC9B929006: public key "Zhang Xin (Ubuntu Kylin Team) <zhangxin@kylinos.cn>" imported
gpg: Total number processed: 1
gpg:               imported: 1
