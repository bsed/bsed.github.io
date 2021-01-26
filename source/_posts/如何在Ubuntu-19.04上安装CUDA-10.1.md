---
title: "如何在Ubuntu 19.04上安装CUDA 10.1"
categories: [ "Linux" ]
tags: [ "ubuntu","cuda" ]
draft: false
slug: "ubuntu-1904-install-cuda-101"
date: "2019-06-23 20:16:00"
---

## 如何在Ubuntu 19.04上安装CUDA 10.1
### 介绍

Ubuntu 19.04已经进入正式发布了。我决定安装它并尝试一下。我的初步印象是感觉它已经针对性能进行了优化。这是第一个使用新5.0内核的Linux发行版。

这是xx.04版本，不是LTS（长期支持）版本。这是一个短期版本，将支持1年。在目前的LTS，Ubuntu 18.04之后两年，下一个LTS版本将是20.04。**为了能够在生产环境中部署，我仍然强烈建议使用Ubuntu 18.04。**

## 在Ubuntu 19.04 上输出 TensorFlow


<!--more-->


```bash
kinghorn@u19:~$ lsb_release -a

Distributor ID:	Ubuntu
Description:	Ubuntu Disco Dingo (development branch)
Release:	19.04
Codename:	disco
```

```bash
kinghorn@u19:~$ uname -a
Linux u19 5.0.0-7-generic #8-Ubuntu SMP Mon Mar 4 16:27:25 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
```
```bash
kinghorn@u19:~$ gcc --version
gcc (Ubuntu 8.3.0-3ubuntu1) 8.3.0
```

```bash
kinghorn@u19:~$ nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2019 NVIDIA Corporation
Built on Fri_Feb__8_19:08:17_PST_2019
Cuda compilation tools, release 10.1, V10.1.105
```
```bash
kinghorn@u19:~$ docker run --runtime=nvidia -u $(id -u):$(id -g) --rm -it tensorflow/tensorflow:2.0.0a0-gpu-py3 bash

________                               _______________                
___  __/__________________________________  ____/__  /________      __
__  /  _  _ _  __ _  ___/  __ _  ___/_  /_   __  /_  __ _ | /| / /
_  /   /  __/  / / /(__  )/ /_/ /  /   _  __/   _  / / /_/ /_ |/ |/ / 
/_/    ___//_/ /_//____/ ____//_/    /_/      /_/  ____/____/|__/


You are running this container as user with ID 1000 and group 1000,
which should map to the ID and group for your user on the Docker host. Great!

tf-docker / > python -c "import tensorflow as tf; print(tf.__version__)"
2.0.0-alpha0
```

## 在Ubuntu 19.04上安装CUDA 10.1的步骤

### 步骤1）安装Ubuntu 19.04！

这个就不列出来了。

### 步骤2）安装NVIDIA驱动程序

安装 `NVIDIA驱动程序410或更高版本` 后 才能使用`CUDA 10.1`。否则你会得 `**Status: CUDA driver version is insufficient for CUDA runtime version**`。我建议使用最新的驱动程序。从 “graphics-drivers ppa” 源安装驱动程序。
```bash
sudo add-apt-repository ppa:graphics-drivers/ppa
```
安装内核编译系统依赖，
```bash
sudo apt-get install dkms build-essential
```
然后安装驱动程序，（418是本文撰写时的版本。）
```bash
sudo apt-get update
sudo apt-get install nvidia-driver-418
```
安装驱动程序后继续并重新启动。
```bash
sudo shutdown -r Now
```
### 步骤3）安装CUDA“依赖项”
完整的 `CUDA.deb` 文件时会安装一些依赖项，但由于我们不打算使用deb文件，因此您需要单独安装它们（四个软件包）。
```bash
sudo apt-get install freeglut3 freeglut3-dev libxi-dev libxmu-dev
```
这些软件包将获得所需的`GL`，`GLU`，`Xi`，`Xmu`库以及其他几个将作为依赖项安装的库。

### 步骤4）获取CUDA.run 文件安装程序（使用Ubuntu 18.10安装程序）
转到[CUDA Zone](https://developer.nvidia.com/cuda-zone)，然后单击立即下载按钮。然后单击链接按钮，直到获得以下内容，

![pic_disp.jpg][1]
下载那个。

### 步骤5）运行“runfile” 文件安装CUDA Toolkit 和 Samples
 我们将 CUDA developer toolkit 套件 安装到系统。您可以使用`sh`来运行shell脚本（“.run”文件）。
```bash
sudo sh cuda_10.1.105_418.39_linux.run
```
这是新安装程序，启动速度与旧版相比有点儿慢。

你需要 接受EULA，之后您将获得“select”。取消选中“Driver”块，然后选择“Install”并点击“Enter”。

```
┌──────────────────────────────────────────────────────────────────────────────┐
│ CUDA Installer                                                               │
│ - [ ] Driver                                                                 │
│      [ ] 418.39                                                              │
│ + [X] CUDA Toolkit 10.1                                                      │
│   [X] CUDA Samples 10.1                                                      │
│   [X] CUDA Demo Suite 10.1                                                   │
│   [X] CUDA Documentation 10.1                                                │
│   Install                                                                    │
│   Options                                                                    │
│                                                                              │
│                                                                              │
│                                                                              │
│                                                                              │
│                                                                              │
│                                                                              │
│                                                                              │
│                                                                              │
│                                                                              │
│                                                                              │
│                                                                              │
│                                                                              │
│                                                                              │
│ Up/Down: Move | Left/Right: Expand | 'Enter': Select | 'A': Advanced options │
└──────────────────────────────────────────────────────────────────────────────┘
```
安装完成后。它会，

- 在 /usr/local/cuda-10.1 目录下安装 CUDA toolkit 
- 创建 `/usr/local/cuda`软连接 
- 在/usr/ local/cuda/samples 目录下 安装 例子 `NVIDIA_CUDA-10.1_Samples`.
- 在下面路径添加 library 路径
```bash
cat /etc/ld.so.conf.d/cuda-10-1.conf 
/usr/local/cuda-10.1/targets/x86_64-linux/lib
```
它没有为工具包设置PATH, 需要我们自己设置 见`步骤6)`。

### 步骤6）设置环境变量
有两种设置环境变量的好方法，之后你就可以使用CUDA。

- 设置系统环境
- 设置用户环境

在过去，我通常会进行系统范围的环境配置。您也可以为单个用户工作站执行此操作。

#### 系统环境替代方案
要为系统上的所有用户（和应用程序）配置CUDA环境，请创建该文件（使用sudo和您选择的文本编辑器）
```bash
vi /etc/profile.d/cuda.sh
```
具有以下内容，
```bash
export PATH=$PATH:/usr/local/cuda/bin
export CUDADIR=/usr/local/cuda
```

启动终端或登录时，本地.bashrc文件会读取/etc/profile.d/中的环境脚本。这是自动的。

下次登录shell时，将从您的路径上的CUDA开始，并可以好使用。如果你想立即在shell中加载该环境而不注销，那么就这样做，
```bash
source /etc/profile.d/cuda.sh
```
**LIBRARY PATH** 注意事项：

`cuda-toolkit`安装后会在`/etc/ld.so.conf.d`中添加了一个`.conf`文件，但它添加的内容似乎并不总是正常工作。如果您正在进行系统环境配置，建议您执行以下操作;

备份 `cuda-10-1.conf`文件，
```bash
sudo mv /etc/ld.so.conf.d/cuda-10-1.conf  /etc/ld.so.conf.d/cuda-10-1.conf-orig
```
然后创建文件
```bash
sudo vi /etc/ld.so.conf.d/cuda.conf
```
包含以下内容，
```bash
/usr/local/cuda/lib64
```
然后运行
```bash
sudo ldconfig
```

`/etc/ld.so.conf.d/`中的这个`cuda.conf`文件将指向`/us/local`中`cuda-xx`的符号链接，所以即使你改变cuda版本后它仍然是正确的工作。（这是我为CUDA设置系统范围环境的“常规”方式。）

#### 用户环境替代方案
如果您希望能够仅在您需要的时间和地点激活您的CUDA环境，那么这是一种方法。您可能更喜欢这种方法而不是系统范围的环境，因为它可以使您的PATH更清晰，并且可以轻松管理多个CUDA版本。如果你决定使用这篇文章中的想法来安装另一个CUDA版本，比如说9.2，以及你的10.1，那么这将更容易来回切换。

对于本地化用户CUDA环境，请创建以下简单脚本。您不需要为此使用sudo，您可以将脚本保留在主目录中的任何位置。当您需要CUDA开发环境时，您只需要“获取”它。

我将创建名为`cuda10.1-env`的文件。将以下行添加到此文件中
```bash
export PATH=$PATH:/usr/local/cuda-10.1/bin
export CUDADIR=/usr/local/cuda-10.1
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-10.1/lib64
```
注意：我明确地使用了`10.1`的版本完整命名路径，即`/usr/local/cuda-10.1`而不是符号链接`/usr/local/cuda`。如果需要，可以使用符号链接路径。我刚刚这样做，以防我想安装另一个版本的CUDA，并使另一个环境脚本指向不同的版本。

现在，当你想要你的CUDA开发环境时，只需要`source cuda10.1-env`。这将在当前shell中设置这些环境变量。（您可以将该文件复制到您的工作目录，或者在使用`source`命令时给出它的完整路径。）

### 步骤7）通过构建“样本”来测试CUDA
让我们确保一切正常。您可以使用安装程序在`NVIDIA_CUDA-10.1_Samples`下的主目录中放置的样本副本，或者从`/usr/local/cuda/samples`复制样本。
```bash
cd  ~/NVIDIA_CUDA-10.1_Samples
source cuda-10.1-env
make -j4
```
运行make命令将编译并链接Makefile中指定的所有源示例。（`-j4`只意味着运行4个“job”make 可以并行构建对象，因此您可以通过使用更多进程来加快构建时间。）

在所有内容完成构建后，您可以`cd`到`bin/x86_64/linux/release/`并查看所有示例可执行文件。即使这是一个不受支持的Ubuntu版本，所有示例似乎都没有错误地构建。我运行了几个程序，他们按预期工作，包括那些使用OpenGL图形的程序。

仅仅因为建立好的样品并不意味着安装没有任何问题，但这是一个非常好的迹象，表明您可以对开发工作充满信心！


## 建议
Ubuntu 19.04看起来它将是一个很好的Linux平台，它拥有所有最新的软件包，这对你很有吸引力。如果你想尝试一个前沿的开发环境，我认真的建议是这样做，否则坚持使用Ubuntu 18.04。您稳定的“生产”平台应该是Ubuntu 18.04。18.04将再支持几年，这意味着它将成为软件构建的一个有吸引力的默认Linux平台。它应该保持稳定并得到很好的支持。


  [1]: https://imgs.gnux.cn/usr/uploads/2019/06/4122286883.jpg