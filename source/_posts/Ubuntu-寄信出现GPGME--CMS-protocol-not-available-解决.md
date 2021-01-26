---
title: "Ubuntu 寄信出现GPGME: CMS protocol not available 解决"
categories: [ "Linux" ]
tags: [ "mail","mutt" ]
draft: false
slug: "linux-mutt-send-letters-appear-gpgme-cms-protocol-not-available-solution"
date: "2017-10-28 18:02:00"
---

Ubuntu 寄信出现GPGME: CMS protocol not available 错误。

避免每次执行都出现GPGME: CMS protocol not available 的讯息，只要于执行此设定下述即可：
```bash
vim ~/.muttrc
set crypt_use_gpgme = no
测试：echo "Hello World" | mutt -s "Hello3" -c 284178359@qq.com
$ mutt -D # 列出所有设定值
$ mutt -D | grep use_gpg
$ mutt -y # 可以用此来看有没有警告讯息
```


<!--more-->


注：Mutt 我只用在收信和发本机的Mail，所以不需要GPG