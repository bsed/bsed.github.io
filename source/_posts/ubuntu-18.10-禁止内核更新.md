---
title: "ubuntu 18.10 禁止内核更新"
categories: [ "Linux" ]
tags: [ "ubuntu","kernel" ]
draft: false
slug: "ubuntu-1810-prohibits-kernel-update"
date: "2018-10-28 08:35:00"
---

禁止内核更新
```bash
sudo apt-mark hold linux-image-generic linux-headers-generic
```

更新系统
```bash
sudo apt-get update
sudo apt-get upgrade -y
```


<!--more-->


启用内核更新
```bash
sudo apt-mark unhold linux-image-generic linux-headers-generic
```

临时更新内核（只更新一次）
```bash
sudo apt-mark hold linux-image-generic linux-headers-generic && sudo apt-get update && sleep 1 && sudo apt-get upgrade -y && sudo apt-mark unhold linux-image-generic linux-headers-generic
```

