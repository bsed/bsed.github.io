---
title: "在Ubuntu上用Docker运行Linux容器(LCX)？"
categories: [ "Golang" ]
tags: [ "docker" ]
draft: false
slug: "running-linux-containers-lcx-with-docker-on-ubuntu"
date: "2014-06-17 12:13:00"
---

虽说标准的硬件虚拟化技术（比如KVM、Xen或Hyper-V）擅长于在一个物理主机上运行多个操作系统的完全隔离的实例，但这种虚拟化技术在性能、资源和资源配置时间等方面存在各种各样的开销。标准的机器虚拟化实际上可能没有必要，这取决于你的实际使用场合。

另外一种轻型虚拟化方法就是所谓的Linux容器（LXC），它提供了操作系统级别的虚拟化。由于不存在运行虚拟机带来的开销，LXC让用户可以在轻型容器沙盒里面运行标准Linux操作系统的多个实例。如果你搭建一个可复制的开发/测试环境，或者在安全沙盒里面部署应用程序，容器就派得上大用场。

Docker就是为了便于部署Linux容器而开发的这样一款开源工具。Docker正迅速成为容器技术方面的一项事实上的标准，已经被诸如Ubuntu和红帽之类的各大Linux发行版所采用。

我在本教程中将演示如何在Ubuntu 14.04上，借助Docker管理Linux容器。请注意：对Ubuntu的早期版本而言，操作步骤可能略有不同。

## 安装Docker


<!--more-->


借助apt-get命令，安装Docker是件轻而易举的事。

    $ sudo apt-get install docker.io 

为了允许非根用户也可以运行Docker，将你自己添加到docker群组。下面这个命令会允许当前用户运行Docker，无需根用户权限。

    $ sudo usermod -a -G docker $USER 

退出，然后重新登录，以激活群组成员的变化。

下一步，编辑Docker配置文件，以便更新Docker二进制代码的位置。

    $ sudo vi /etc/default/docker.io 
    DOCKER="/usr/bin/docker.io" 

## 重启Docker服务。

    $ sudo service docker.io restart 

## 管理Docker容器

如果你想启动Ubuntu操作系统的一个新的Docker容器，首先需要获取Ubuntu Docker映像文件。下面这个命令会通过网络下载Docker映像文件。

    $ docker pull ubuntu 

你可以以一种交互模式来开启Ubuntu Docker，如下所示。最后一个参数“/bin/bash”是一旦启动就将在容器里面执行的命令，这里是一个简单的bash外壳命令。

    $ docker run -i -t ubuntu /bin/bash 

上述命令会立即启动一个Ubuntu容器（这正是容器的魅力所在！），并为你提供容器里面的外壳提示符。这时候，你应该能够访问沙盒环境里面的标准的Ubuntu操作系统了。

想退出Docker容器，在容器里面的提示符处键入“exit”。

你可以启动不同形式的容器。比如，想启动Fedora容器，请执行下面这个命令：

    $ docker.io run -i -t fedora /bin/bash 

如果本地没有Fedora Docker映像文件，该命令就会首先自动下载映像文件，然后启动Docker。

如果你想启动采用某个发行版版本的容器，也可以这么做。比如说，想启动Ubuntu 13.04 Docker，请执行下面这个命令：

    $ docker.io run -i -t ubuntu:13.04 /bin/bash 

## 容器网络

Docker使用Linux网桥将容器彼此互联起来，并将它们连接到外部网络。安装了Docker后，你应该会看到默认情况下自动组建的docker0 Linux网桥。你创建的每个容器都将连接到docker0网桥接口。

## 自定义Linux网桥

如果你想，也可以使用自定义Linux网桥将诸容器互联起来。为此，你可以建立一个自定义网桥，并对它进行配置，如下所示。你可以为该网桥分配一个单独的子网，并且从子网为Docker分配IP地址。我会使用10.0.0.0/24作为Docker子网。

    $ sudo apt-get install bridge-utils 
    $ sudo brctl addbr br0 
    $ sudo ifconfig br0 10.0.0.1 netmask 255.255.255.0 

想让Docker使用自定义网桥，将“-b=br0”添加到/etc/default/docker.io中的DOCKER_OPTS变量，然后重启Docker服务。

    $ sudo service docker.io restart 

至此，任何新的容器都会连接到br0，其IP地址会自动从10.0.0.0/24来分配。

## 其他定制

还有另外几种方法可以定制Docker的默认网络设置，主要是通过改动/etc/default/docker.io中的DOCKER_OPTS变量来实现。

    “-dns 8.8.8.8 -dns 8.8.4.4”：指定容器使用的DNS服务器。
    “-icc=false”：让诸容器彼此隔离开来。

## 故障排查

1. 运行docker.io命令时，你会遇到下面这个错误。

    dial unix /var/run/docker.sock: no such file or directory（没有此类文件或目录） 

出现这个错误，可能是由于Docker守护程序没在运行。检查Docker守护程序的状态，确保先启动它。

    $ sudo service docker.io status 
    $ sudo service docker.io start 

原文链接：[http://xmodulo.com/2014/05/manage-linux-containers-docker-ubuntu.html](http://xmodulo.com/2014/05/manage-linux-containers-docker-ubuntu.html)
