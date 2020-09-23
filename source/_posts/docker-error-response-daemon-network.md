---
title: docker: Error response from daemon: Cannot link to /redis, as it does not belong to the default network
date: 2019-10-11 21:18:00
updated: 2019-10-11 21:19:47
tags: 
- android
- archlinux
- fusermount
- mtp
categories: 
- linux

---
![87385-uldlqx4cb7c.png](https://imgs.gnux.cn/usr/uploads/2019/10/1544421395.png)

问题描述：
使用docker-compose启动多个容器后，又单独run container，同时连接到docker-compose启动的容器，


<!--more-->


命令：
```bash
╰─sudo docker run --link redis:redis --name console ubuntu bash
```
报错：
```bash
docker: Error response from daemon: Cannot link to /redis, as it does not belong to the default network.
ERRO[0002] error waiting for container: context canceled 
```

查看网络
```bash
╰─sudo docker network ls
```
![36991-521s6gnwdiv.png](https://imgs.gnux.cn/usr/uploads/2019/10/1583184751.png)


 问题原因是Docker-Compose命名约定造成。

使用docker-compose时，您的网络名称是根据 docker-compose “项目名称”决定的，该项目名称基于其所在目录的名称。您可以使用`-project-name标志覆盖项目名称`或 `COMPOSE_PROJECT_NAME环境变量`。 我的`docker-compose.yml`文件位于一个名为**allindocker**的目录中，这就是为什么docker-compose在创建正在运行的容器实例时选择了`allindocker_default`！

## 解决办法：
run 命令后增加--net参数，根据`docker-compose` 网络

```bash
╰─sudo docker run --link redis:redis --net allindocker_default --name console ubuntu bash
```