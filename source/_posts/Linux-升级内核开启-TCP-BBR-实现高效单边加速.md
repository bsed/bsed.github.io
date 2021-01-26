---
title: "Linux 升级内核开启 TCP BBR 实现高效单边加速"
categories: [ "Linux" ]
tags: [ "linux","ubuntu","bbr" ]
draft: false
slug: "the-linux-upgrade-kernel-opens-tcp-bbr-to-achieve-efficient-single-sided-acceleration"
date: "2017-05-18 08:10:00"
---

# 前言

自从锐速发布以来，这款牛逼的单边加速神器的确为一些线路不太优秀的服务器带来了更优秀的体验。但是呢，过高的价格和不再低端售卖。导致了我们并无法实现一个免费好用的单边加速功能。

但是捏，谷歌为我们带来了干货 **新的 TCP 拥塞控制算法 BBR (Bottleneck Bandwidth and RTT)**。 目前在 Linux Kernel 4.9 中加入了该算法，所以我们只要升级内核就可以爽一波了。

难懂的文言文，我们也不说了，TCP BBR 的目的就是要尽量跑满带宽，并且尽量不要有排队的情况。

下面介绍一下如何在 RHEL、CentOS、Debian 和 Ubuntu 还有其他系统开启 TCP BBR。


<!--more-->


**注：** 更新内核有一定风险，请注意备份。

# 开启 TCP BBR

只要 Linux 发行版的 Kernel 即内核版本大于等于 4.9 即可开启，开启方法是通用的，如何升级至 Kernel 将在下面介绍。

**修改系统变量：**

```
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf

```

**保存生效**

```
sysctl -p
```

**执行**

```
sysctl net.ipv4.tcp_available_congestion_control
```

如果结果是这样

```
 "root@debian-512mb-sgp1-01:~# sysctl net.ipv4.tcp_available_congestion_control
net.ipv4.tcp_available_congestion_control = bbr cubic reno

```

就开启了。

执行  `lsmod | grep bbr`，以检测 BBR 是否开启。

# Debian、Ubuntu 升级内核至 4.9rc

**内核下载地址：**

> Debian 64位：[http://mirrors.kernel.org/debian/pool/main/l/linux/linux-image-4.9.0-rc8-amd64-unsigned_4.9~rc8-1~exp1_amd64.deb](http://mirrors.kernel.org/debian/pool/main/l/linux/linux-image-4.9.0-rc8-amd64-unsigned_4.9~rc8-1~exp1_amd64.deb)
> Debian 32位：[http://mirrors.kernel.org/debian/pool/main/l/linux/linux-image-4.9.0-rc8-686-unsigned_4.9~rc8-1~exp1_i386.deb](http://mirrors.kernel.org/debian/pool/main/l/linux/linux-image-4.9.0-rc8-686-unsigned_4.9~rc8-1~exp1_i386.deb)
> Ubuntu 64位：[http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.9-rc8/linux-image-4.9.0-040900rc8-generic_4.9.0-040900rc8.201612051443_amd64.deb](http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.9-rc8/linux-image-4.9.0-040900rc8-generic_4.9.0-040900rc8.201612051443_amd64.deb)
> Ubuntu 32位： [http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.9-rc8/linux-image-4.9.0-040900rc8-generic_4.9.0-040900rc8.201612051443_i386.deb](http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.9-rc8/linux-image-4.9.0-040900rc8-generic_4.9.0-040900rc8.201612051443_i386.deb)

这里以 Debian 8 amd64 为例，其他 32bit 或者 Ubuntu 系统只要修改内核 deb 软件名称即可。

**一、下载内核**

```
wget http://mirrors.kernel.org/debian/pool/main/l/linux/linux-image-4.9.0-rc8-amd64-unsigned_4.9~rc8-1~exp1_amd64.deb

```

**二、安装内核**

```
dpkg -i linux-image-4.9.0-rc8-amd64-unsigned_4.9~rc8-1~exp1_amd64.deb

```

**三、查看已经安装的内核**

```
dpkg -l|grep linux-image 

```

会反馈如下：

```
ii  linux-image-3.16.0-4-amd64           3.16.36-1+deb8u2                     amd64        Linux 3.16 for 64-bit PCs
ii  linux-image-4.9.0-rc8-amd64-unsigned 4.9~rc8-1~exp1                       amd64        Linux 4.9-rc8 for 64-bit PCs
ii  linux-image-amd64                    3.16+63                              amd64        Linux for 64-bit PCs (meta-package)

```

**四、卸载旧内核**

```
apt-get remove `内核名`

```

**五、更新引导并重启**

```
update-grub  #更新引导
reboot  #重启

```

然后我们可以查看一下目前的内核版本：

```
root@debian-512mb-sgp1-01:~# uname -r
4.9.0-rc8-amd64

```

可见已经是最新的了。

# RHEL、CentOS 升级内核至 4.9rc

因为 elrepo 方面并没有推出已经封装好的内核，所以我们这里借用 Debian 的内核为例。
这里以 CentOS 7 64bit 为例，教程需要系统最少 CentOS6

**一、下载内核二进制包**

```
wget http://mirrors.kernel.org/debian/pool/main/l/linux/linux-image-4.9.0-rc8-amd64-unsigned_4.9~rc8-1~exp1_amd64.deb 
```

**二、解压安装**

```
ar x linux-image-4.9.0-rc8-amd64-unsigned_4.9~rc8-1~exp1_amd64.deb
tar -Jxf data.tar.xz
install -m644 boot/vmlinuz-4.9.0-rc8-amd64 /boot/vmlinuz-4.9.0-rc8-amd64
cp -Rav lib/modules/4.9.0-rc8-amd64 /lib/modules/
depmod -a 4.9.0-rc8-amd64

```

**三、加入引导**

```
dracut -f -v --hostonly -k '/lib/modules/4.9.0-rc8-amd64'  /boot/initramfs-4.9.0-rc8-amd64.img 4.9.0-rc8-amd64
grub2-mkconfig -o /boot/grub2/grub.cfg

```

**四、修改引导顺序**

\1. 查看引导内有哪些内核

```
cat /boot/grub2/grub.cfg |grep menuentry

```

输入结果：

```
[root@centos-512mb-sgp1-01 ~]# cat /boot/grub2/grub.cfg |grep menuentry 
if [ x"${feature_menuentry_id}" = xy ]; then
  menuentry_id_option="--id"
  menuentry_id_option=""
export menuentry_id_option
menuentry 'CentOS Linux (4.9.0-rc8-amd64) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-4.9.0-rc8-amd64-advanced-508f0c60-8ce4-48fa-a00e-8db45fa56da8' {
menuentry 'CentOS Linux (3.10.0-327.36.3.el7.x86_64) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-3.10.0-327.36.3.el7.x86_64-advanced-508f0c60-8ce4-48fa-a00e-8db45fa56da8' {
menuentry 'CentOS Linux (0-rescue-d45b6a27fe9641bd8979101342a4f20b) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-0-rescue-d45b6a27fe9641bd8979101342a4f20b-advanced-508f0c60-8ce4-48fa-a00e-8db45fa56da8' {

```

2.配置从默认内核启动，下面命令的内核名称根据系统内部查到的实际名称来替换：

```
grub2-set-default 'CentOS Linux (4.9.0-rc8-amd64) 7 (Core)'

```

3.验证是否配置成功：

```
grub2-editenv list 

```

输入结果：

```
saved_entry=CentOS Linux (4.9.0-rc8-amd64) 7 (Core)

```

重启就可以完成更新内核了！

原文：https://www.mf8.biz/linux-kernel-with-tcp-bbr/