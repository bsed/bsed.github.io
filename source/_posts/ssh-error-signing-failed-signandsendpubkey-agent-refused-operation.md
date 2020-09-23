---
title: ssh出错 sign_and_send_pubkey: signing failed: agent refused operation
date: 2015-03-28 22:28:00
updated: 2016-11-19 22:28:54
tags: 
- linux
- iptables
categories: 
- linux

---
在服务器添加完公钥之后，ssh服务器然后报了这个错误

sign_and_send_pubkey: signing failed: agent refused operation

然后执行了以下命令才好。。
```bash
eval "$(ssh-agent -s)"
ssh-add
```