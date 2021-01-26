---
title: "在Ubuntu 18.04 多显卡环境下，使用intel显示 、用nvidia计算 [翻译]"
categories: [ "Linux" ]
tags: [ "ubuntu","nvidia","intel" ]
draft: false
slug: "in-multigraphics-card-environment-using-intel-display-and-nvidia-computing"
date: "2019-06-28 13:37:00"
---

本指南将向您展示如何在Ubuntu 18.04桌面上使用英特尔驱动渲染显示和用NVIDIA驱动进行CUDA计算。

我在普通的游戏PC上完成了这项工作，它配备了两个图形设备，一个**Intel UHD Graphics 630**和一个**NVIDIA GeForce GTX 1080 Ti**。它们都可以通过显示`lspci | grep VGA`。

```
00:02.0 VGA compatible controller: Intel Corporation Device 3e92
01:00.0 VGA compatible controller: NVIDIA Corporation GP102 [GeForce GTX 1080 Ti] (rev a1)
```

本指南可归纳为两个步骤：

1. 在没有OpenGL文件的情况下安装NVIDIA驱动程序。
2. 配置Xorg以使用Intel图形。


<!--more-->


我没有测试过不同的硬件配置，但它的工作方式应该相似。有关更多讨论，请参阅[本节](https://gist.github.com/wangruohui/bc7b9f424e3d5deb0c0b8bba990b1bc5#Discussion)。

## 0.准备

在Linux中运行之前，您需要在硬件上做一些准备工作。确保显示器已插入主板上的连接器，而不是专用显示卡上的连接器。配置BIOS以将Intel图形作为主显示设备（通常选择**IGFX**而不是**PEG**或**PCIE**）。确保您的计算机可以启动到GUI并在此设置下成功登录到桌面。

## 1.安装没有OpenGL文件的NVIDIA驱动程序

我建议用以下方式在以太网中安装驱动程序。如果您想按照自己的方法，只需确保没有安装OpenGL文件。

### 1.1。卸载以前的所有安装

两种方法的共同步骤，以避免可能的冲突。

1. 如果您已通过PPA存储库安装

   ```bash
   sudo apt purge nvidia*
   ＃一些互动操作
   sudo apt autoremove
   ＃一些互动操作
   ```

   检查与NVIDIA相关的剩余包。

   ```bash
   dpkg -l | grep nvidia
   ```

   如果某些包未被删除，请手动将其删除

   ```bash
   sudo dpkg -P < package_name >
   ```

   如果您已添加第三方存储库，例如来自NVIDIA的存储库，请将其删除。这可以通过删除`/etc/apt/source.list.d/`或通过`ppa-purge`实用程序删除相关文件来完成。

2. 如果您已通过二进制安装程序安装

   ```bash
   sudo nvidia-uninstall
    ＃一些互动操作
   ```

3. 重启。

### 1.2.A. 从PPA存储库安装

1. 添加`ppa:graphics-drivers/ppa`存储库。

   ```bash
   sudo add-apt-repository ppa：graphics-drivers / ppa
    ＃一些交互操作
   ```

   在`sudo apt update`添加PPA后，ubuntu 18.04 会自动执行，因此不需要手动操作。

2. 安装**headless** NVIDIA驱动程序

   ```bash
   sudo apt install nvidia-headless-418 nvidia-utils-418
   ```

   **重要**

   `nvidia-headless-418`仅包含驱动程序，而`nvidia-driver-418`包含所有，包括显示部件，例如OpenGL库。如果您希望将显示器连接到NVIDIA显卡，请安装完整的包，否则，只安装驱动程序。

   `nvidia-utils-418`软件包提供了诸如`nvidia-smi`。

3. 重启。如果安装成功，命令`nvidia-smi`将显示所有NVIDIA GPU。

### 1.2.B. 从二进制安装程序安装

[我以前的帖子](https://gist.github.com/wangruohui/df039f0dc434d6486f5d4d098aa52d07#creat-blacklist-for-nouveau-driver)，老了，详细而且仍然有效。

总结：

1. 从[NVIDIA官方站点](https://www.nvidia.com/object/unix.html)下载二进制安装程序并使其可执行。

2. 通过创建`/etc/modprobe.d/blacklist-nouveau.conf`包含内容的文件来禁用nouveau驱动程序

   ```
   blacklist nouveau
   options nouveau modeset=0
   ```

   或只是执行安装程序，它会提示为您创建它。

3. 执行`sudo update-initramfs -u`然后重新启动。

4. 启动后，切换到tty1。停止显示等服务`gdm`，`gdm3`，`lightdm`。杀死使用图形卡的所有进程，例如`Xorg`或`X`。

5. 执行带`--no-opengl-files`后缀的安装程序（**重要**）以避免安装所有与OpenGL相关的文件，例如

   ```bash
   sudo ./NVIDIA-Linux-x86_64-418.56.run --no-opengl-files
   ```

   或者，如果您想从NVIDIA显卡中显示，请执行安装程序，不需要任何参数，例如

   ```bash
   sudo ./NVIDIA-Linux-x86_64-418.56.run
   ```

6. 安装成功后，命令`nvidia-smi`将显示所有NVIDIA GPU。

## 2.配置Xorg

安装的NVIDIA驱动程序和配置将暗示Xorg从NVIDIA设备开始。根据NVIDIA相关显示库是否安装良好，X服务器无法启动或成功启动但仍使用NVIDIA设备，这两者都是不需要的。

我们可以通过创建包含以下内容的配置文件来强制Xorg使用英特尔图形并将其保存到`/etc/X11/xorg.conf`。

```
Section "ServerLayout"
    Identifier     "Layout0"
    Screen      0  "Screen0"
EndSection

Section "Screen"
    Identifier     "Screen0"
    Device         "Device0"
EndSection

Section "Device"
    Identifier     "Device0"
    Driver         "intel"
    VendorName     "Intel Corporation"
    BusID          "PCI:0:2:0
EndSection
```

关键点是“BusID”选项。它表示Intel图形连接的PCI总线ID。它可以从中检索`lspci`。例如，在我的计算机上，`lspci`输出`00:02.0 VGA compatible controller: Intel Corporation Device 3e92`，因此总线ID是`0:2:0`。

请注意，总线ID输出`lspci`为十六进制，但填写的数字`xorg.conf`应为十进制。例如，当该输出`lspci`是`82:00.0 VGA com...`，您需要填写`PCI:130:0:0`在配置中。

由于我只有一台显示器，配置非常简单。如果您的显示布局不同，则需要相应地进行配置。无论如何，搜索互联网以获得适当的配置。`man xorg.conf`和[ArchLinux wiki](https://wiki.archlinux.org/index.php/Xorg)是很好的参考。

配置完成后，重新启动计算机。如果成功，您将能够登录桌面。如果失败，您可能会被锁定在登录屏幕上。重新启动到高级模式，下拉到根提示符并尝试检查`/var/log/Xorg.*.log.*`提示。目前我对此几乎没有调试经验。

成功登录后。打开终端并执行`glxheads`，显示的渲染设备应为Intel图形。

```
glxheads: exercise multiple GLX connections (any key = exit)
Usage:
  glxheads xdisplayname ...
Example:
  glxheads :0 mars:0 venus:1
Name: :0
  Display:     0x56151a1570e0
  Window:      0x2000002
  Context:     0x56151a182f60
  GL_VERSION:  3.0 Mesa 18.0.5
  GL_VENDOR:   Intel Open Source Technology Center
  GL_RENDERER: Mesa DRI Intel(R) HD Graphics (Coffeelake 3x8 GT2)
```

检查`nvidia-smi`，应该没有`Xorg`列出进程，内存占用应该是`0MB`。
![54079-0b8ighvmmlks.png](https://imgs.gnux.cn/usr/uploads/2019/06/3755975054.png)
```
Thu Nov 22 07:05:55 2018
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 418.56       Driver Version: 418.56       CUDA Version: 10.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX 108...  Off  | 00000000:01:00.0 Off |                  N/A |
|  0%   52C    P0    57W / 250W |      0MiB / 11178MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```

执行繁重的GPU程序并在计算过程中移动鼠标。显示应该是平滑的，因为计算和显示渲染在不同的设备上分开。

恭喜！

如果`glxheads`输出一些虚拟图形和`nvidia-smi`仍然输出`Xorg`过程，您可能仍然使用NVIDIA图形进行渲染。它只是将渲染图像传递给英特尔图形进行显示。运行繁重的GPU程序时，显示仍然会很慢。

## 3.讨论

分离显示和计算对于顺利工作非常重要。

1. 您可以使用远程headless服务器来计算本地客户端以进行显示。您可以通过SSH连接远程服务器。这可能是最简单的方法。在此设置下，正确安装的headless驱动程序将使一切正常。您无需遵循本指南。
2. 如果您只有一台计算机，并且计算机中只有一台图形设备。不幸的是。您必须使用唯一的设备执行所有任务，并且在运行GPU程序时会遇到严重的显示延迟。
3. 您使用一台计算机进行计算和显示。有两个（或更多）图形设备，它们来自同一供应商。然后问题就变得容易了。以NVIDIA为例，通过正确安装驱动程序，可以完美地分离显示和计算。可以将监视器插入设备0并使用`CUDA_VISIBIE_DEVICE=1`环境标志在设备1上执行计算。您可能不需要本指南。
4. 您使用一台计算机进行计算和显示。有两个（或更多）图形设备，但它们来自不同的供应商。例如，普通的游戏PC配置可以包括CPU内的Intel HD Graphics和NVIDIA的专用GPU。需要将显示器插入主板以使用Intel显示器并在NVIDIA上运行CUDA程序。那你就在正确的地方。
5. 出于某种目的，您需要一个NVIDIA GPU来进行计算和渲染。然后你可能需要在一个GPU上运行它们。您需要安装支持OpenGL的drvier并在Xorg中调整一些渲染设置。

原文：[https://gist.github.com/wangruohui/bc7b9f424e3d5deb0c0b8bba990b1bc5](https://gist.github.com/wangruohui/bc7b9f424e3d5deb0c0b8bba990b1bc5)