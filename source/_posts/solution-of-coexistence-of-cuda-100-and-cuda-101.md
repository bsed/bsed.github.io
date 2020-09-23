---
title: cuda10.0与cuda10.1并存解决方案
date: 2019-06-30 15:25:00
updated: 2020-01-16 13:33:55
tags: 
- html5
categories: 
- css

---
## 安裝CUDA10.1

![Screenshot_2019-06-30_15-13-36.png][1]

download: [https://developer.nvidia.com/cuda-10.1-download-archive-base](https://developer.nvidia.com/cuda-10.1-download-archive-base)

详细安装教程看这里: [https://tygasoft.com/ubuntu-1904-install-cuda-101.html](https://tygasoft.com/ubuntu-1904-install-cuda-101.html)

**需要特别注意的是**，笔者 nvidia 驱动安装的是无显卡版本的(`sudo apt install nvidia-headless-4xx nvidia-utils-4xx`)，nvidia 960 只使用他的gpu 运算能力。 笔者最近已经升级为430版本。 详细见:[https://tygasoft.com/in-multigraphics-card-environment-using-intel-display-and-nvidia-computing.html](https://tygasoft.com/in-multigraphics-card-environment-using-intel-display-and-nvidia-computing.html)


<!--more-->


## 安裝CUDA10.0

download : [https://developer.nvidia.com/cuda-10.0-download-archive](https://developer.nvidia.com/cuda-10.0-download-archive)

### 首先下載CUDA：
```bash
wget https://developer.nvidia.com/compute/cuda/10.0/Prod/local_installers/cuda_10.0.130_410.48_linux
```
wget xuyaochong 米能米能

### 添加執行權限：
```bash
sudo chmod +x cuda_10.0.130_410.48_linux.run
```
**方法一：**
### 開始安裝CUDA：
```
sudo ./cuda_10.0.130_410.48_linux.run --driver --silent
sudo ./cuda_10.0.130_410.48_linux.run --toolkit --silent
sudo ./cuda_10.0.130_410.48_linux.run --samples --silent
```
若出現Missing recommended library: `libGLU.so`，則進行下面命令：
```bash
sudo apt-get install libglu1-mesa libxi-dev libxmu-dev libglu1-mesa-dev
```
安裝CUDA之後，還是需要將CUDA添加至~/.bashrc當中：
```bash
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/64:$LD_LIBRARY_PATH
```
然後再激活`~/.bashrc`
```bash
source ~/.bashrc
```

再檢查是否安裝完成：
nvcc -V
出現下面信息表示安裝成功：
$ nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2018 NVIDIA Corporation
Built on Sat_Aug_25_21:08:01_CDT_2018
Cuda compilation tools, release 10.0, V10.0.130

<!--more-->

**方法二：**  笔者采用方法二

### 添加執行權限：
```bash
sudo chmod +x uda_10.0.130_410.48_linux.run
```

### 開始安裝CUDA：
```bash
sudo sh cuda_10.0.130_410.48_linux.run
```
```
$ sudo ./cuda_10.0.130_410.48_linux.run --override
Logging to /tmp/cuda_install_17656.log
Using more to view the EULA.
End User License Agreement
--------------------------


Preface
-------

The Software License Agreement in Chapter 1 and the Supplement
in Chapter 2 contain license terms and conditions that govern
the use of NVIDIA software. By accepting this agreement, you
agree to comply with all the terms and conditions applicable
to the product(s) included herein.


NVIDIA Driver


Description

This package contains the operating system driver and
fundamental system software components for NVIDIA GPUs.


NVIDIA CUDA Toolkit


Description

The NVIDIA CUDA Toolkit provides command-line and graphical
tools for building, debugging and optimizing the performance
of applications accelerated by NVIDIA GPUs, runtime and math
libraries, and documentation including programming guides,
user manuals, and API references.


Default Install Location of CUDA Toolkit

Windows platform:

%ProgramFiles%\NVIDIA GPU Computing Toolkit\CUDA\v#.#

Linux platform:

/usr/local/cuda-#.#

Mac platform:

/Developer/NVIDIA/CUDA-#.#
Do you accept the previously read EULA?
accept/decline/quit: accept

You are attempting to install on an unsupported configuration. Do you wish to continue?
(y)es/(n)o [ default is no ]: y

Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 410.48?
(y)es/(n)o/(q)uit: n 如果之前已经安装过驱动了，在选择安装驱动时，选no

Install the CUDA 10.0 Toolkit?
(y)es/(n)o/(q)uit: y

Enter Toolkit Location
 [ default is /usr/local/cuda-10.0 ]: 

Do you want to install a symbolic link at /usr/local/cuda?
(y)es/(n)o/(q)uit: y

Install the CUDA 10.0 Samples?
(y)es/(n)o/(q)uit: y

Enter CUDA Samples Location
 [ default is /home/kelvin ]: 

Installing the CUDA Toolkit in /usr/local/cuda-10.0 ...
Installing the CUDA Samples in /home/kelvin ...
Copying samples to /home/kelvin/NVIDIA_CUDA-10.0_Samples now...
Finished copying samples.

===========
= Summary =
===========

Driver:   Not Selected
Toolkit:  Installed in /usr/local/cuda-10.0
Samples:  Installed in /home/kelvin

Please make sure that
 -   PATH includes /usr/local/cuda-10.0/bin
 -   LD_LIBRARY_PATH includes /usr/local/cuda-10.0/lib64, or, add /usr/local/cuda-10.0/lib64 to /etc/ld.so.conf and run ldconfig as root

To uninstall the CUDA Toolkit, run the uninstall script in /usr/local/cuda-10.0/bin

Please see CUDA_Installation_Guide_Linux.pdf in /usr/local/cuda-10.0/doc/pdf for detailed information on setting up CUDA.

***WARNING: Incomplete installation! This installation did not install the CUDA Driver. A driver of version at least 384.00 is required for CUDA 10.0 functionality to work.
To install the driver using this installer, run the following command, replacing <CudaInstaller> with the name of this run file:
    sudo <CudaInstaller>.run -silent -driver

Logfile is /tmp/cuda_install_17656.log
Signal caught, cleaning up
```

现在我的机器中既有`CUDA10.0`也有`CUDA10.1`，下面我要切换使用的是CUDA10.0

使用以下命令查看系统环境变量配置：
```bash
mousepad ~/.bashrc
```
CUDA环境变量如下：
```bash
export CUDA_HOME=/usr/local/cuda
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:"$LD_LIBRARY_PATH:/usr/loacl/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export PATH=/usr/local/cuda/bin:$PATH
```
实质上切换CUDA版本就是将cuda10.0或者cuda10.1链接到cuda文件上。

在`/usr/local/`下打开终端，使用如下命令进行切换：
```bash
$ sudo rm -rf cuda
$ sudo ln -s /usr/local/cuda-10.0/ /usr/local/cuda

$ nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2018 NVIDIA Corporation
Built on Sat_Aug_25_21:08:01_CDT_2018
Cuda compilation tools, release 10.0, V10.0.130
```
第一个命令是移除原本存在的名为cuda的软链接，**第二个命令是将cuda10.0链接到一个新的名为cuda的软链接上**。

切换 Cuda 10.1:

在`/usr/local/`下打开终端，使用如下命令进行切换：
```bash
$ sudo rm -rf cuda
$ sudo ln -s /usr/local/cuda-10.1/ /usr/local/cuda

$ nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2019 NVIDIA Corporation
Built on Wed_Apr_24_19:10:27_PDT_2019
Cuda compilation tools, release 10.1, V10.1.168
```
参考链接:

- [https://zhuanlan.zhihu.com/p/59123983](https://zhuanlan.zhihu.com/p/59123983)
- [https://medium.com/@zihansyu/ubuntu-16-04-%E5%AE%89%E8%A3%9Dcuda-10-0-cudnn-7-3-8254cb642e70](https://medium.com/@zihansyu/ubuntu-16-04-%E5%AE%89%E8%A3%9Dcuda-10-0-cudnn-7-3-8254cb642e70)

  [1]: https://imgs.gnux.cn/usr/uploads/2019/06/945605763.png


