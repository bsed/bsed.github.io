---
title: Uninstall Docker from Ubuntu 15.04
date: 2014-11-08 20:43:00
updated: 2016-11-08 20:48:28
tags: 
- ubuntu
- hadoop
categories: 
- bigdata

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