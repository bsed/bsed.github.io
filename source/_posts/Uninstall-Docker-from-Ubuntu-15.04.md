---
title: "Uninstall Docker from Ubuntu 15.04"
categories: [ "Linux" ]
tags: [ "linux","ubuntu","docker" ]
draft: false
slug: "docker-from-ubuntu-uninstall-1504"
date: "2014-11-08 20:43:00"
---

有时候你需要从机器上卸掉docker容器。


<!--more-->


## 卸掉docker

```bash
sudo apt-get remove docker-engine
```

## 移除(config/data)配置文件
```bash
sudo apt-get remove --auto-remove docker-engine
```

## 完全卸掉

```bash
sudo apt-get purge docker-engine
```
或者

```
sudo apt-get purge --auto-remove docker-engine
```