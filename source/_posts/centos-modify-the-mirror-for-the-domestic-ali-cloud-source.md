---
title: Centos修改镜像为国内的阿里云源
date: 2016-11-19 19:01:00
updated: 2016-11-19 19:01:57
tags: 
- linux
- email
- iptables
categories: 
- linux

---
## 阿里云Linux安装软件镜像源
阿里云是最近新出的一个镜像源。得益与阿里云的高速发展，这么大的需求，肯定会推出自己的镜像源。
阿里云Linux安装镜像源地址：[http://mirrors.aliyun.com/](http://mirrors.aliyun.com/)

CentOS系统更换软件安装源

### 备份你的原镜像文件，以免出错后可以恢复。
```bash
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```
### 下载新的 CentOS-Base.repo 到/etc/yum.repos.d/


<!--more-->


**CentOS 5**
```bash
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
```
**CentOS 6**
```bash
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
```
### 运行`yum makecache`生成缓存

```bash
yum clean all
yum makecache
```

## 163安装软件镜像源

163开源镜像站是国内比较老的一个网站。很多人都在使用。从这里就可以看出来，网易，果然是业界良心啊。

网易开源镜像站:[http://mirrors.163.com/](http://mirrors.163.com/)

CentOS系统更换软件安装源

### 备份你的原镜像文件，以免出错后可以恢复。
```bash
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```
### 下载新的CentOS-Base.repo 到/etc/yum.repos.d/

**CentOS 5**
```bash
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS5-Base-163.repo
```
**CentOS 6**

```bash
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS6-Base-163.repo
```
### 运行以下命令生成缓存 
```bash
yum clean all
yum makecache
```
当然，除了网易之外，国内还有其他不错的yum源，比如中科大和搜狐的等，大家可以根据自己需求下载

中科大的Linux安装镜像源：[http://centos.ustc.edu.cn/](http://centos.ustc.edu.cn/)
搜狐的Linux安装镜像源：[http://mirrors.sohu.com/](http://mirrors.sohu.com/)
北京首都在线科技：[http://mirrors.yun-idc.com/](http://mirrors.yun-idc.com/)