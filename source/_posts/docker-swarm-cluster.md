---
title: docker + swarm 集群
date: 2015-06-15 16:14:00
updated: 2017-02-12 16:15:35
tags: 
categories: 
- java

---
## 安装swarm
```bash
docker run --rm swarm create  
6a2d606bb3155b4428d0dc483cff6800
```
## 创建Docker虚拟机
```bash
docker-machine.exe create -d virtualbox swarm-master  
docker-machine.exe create -d virtualbox swarm-node1  
docker-machine.exe create -d virtualbox swarm-node2  
```
## 搭建swarm集群


<!--more-->


### 创建swarm-master
```
eval $(docker-machine.exe env swarm-master)  
docker run -d -p 3376:3376 -t \  
-v /var/lib/boot2docker:/carts:ro swarm manage \
-H 0.0.0.0:3376 \
--tlsverify --tlscacert=/certs/ca.pem \
--tlscert=/certs/server.pem \
--tlskey=/certs/server-key.pem \
token://6a2d606bb3155b4428d0dc483cff6800

docker run -d swarm join --addr=$(docker-machine.exe ip swarm-master):2376 token://6a2d606bb3155b4428d0dc483cff6800  
```
## 创建swarm-node
```
eval $(docker-machine.exe env swarm-node1)  
docker run -d swarm join --addr=$(docker-machine.exe ip swarm-node1):2376 token://6a2d606bb3155b4428d0dc483cff6800

eval $(docker-machine.exe env swarm-node2)  
docker run -d swarm join --addr=$(docker-machine.exe ip swarm-node2):2376 token://6a2d606bb3155b4428d0dc483cff6800
```
## 管理Swarm集群
```
export DOCKER_HOST=$(docker-machine ip swarm-master):3376  
docker info  
```