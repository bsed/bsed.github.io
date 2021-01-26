---
title: "docker安装oracle 11G 数据库："
categories: [ "Linux" ]
tags: [ "docker","oracle" ]
draft: false
slug: "docker-install-oracle-11g-database"
date: "2015-11-28 13:27:00"
---

> 版本： docker-oracle-xe-11g

## 环境：

Oracle Express Edition 11g Release 2 与 Ubuntu 16.04.1 LTS

Dockerfile [地址](https://registry.hub.docker.com/u/wnameless/oracle-xe-11g/) 在 [Docker中央库](https://registry.hub.docker.com/).


<!--more-->


##安装
```bash
docker pull wnameless/oracle-xe-11g  
```
以22(sshd), 1521(oracle)号端口启动容器:
```bash
docker run -d -p 49160:22 -p 49161:1521 wnameless/oracle-xe-11g  
```
数据库连接信息:
```
hostname: localhost  
port: 49161  
sid: xe  
username: system  
password: oracle  
```
oracle的 `SYS` 和 `SYSTEM`用户密码: `oracle`

## 用ssh连接docker容器

```bash
ssh root@localhost -p 49160  
password: admin  
```