---
title: "Windows 10 启动VirtualBox后禁止出现运行界面"
categories: [ "Linux" ]
tags: [ "windows","virtualbox" ]
draft: false
slug: "modelget-netif"
date: "2020-12-14 11:11:00"
---

在Windows下用SSH连接Centos时, Virtualbox启动总会有界面出现，下面为大家提供个不错的解决方法。
不太习惯Centos桌面环境的使用，但喜欢使用Linux命令操作方式和Linxu上的开发或者折腾，在Windows下用XShell连接Centos。

**查看有哪些虚拟机**


<!--more-->

```bat
"D:\Program Files\Oracle\VirtualBox\VBoxManage.exe" list vms
```
**查看虚拟的详细信息**

```bat
"D:\Program Files\Oracle\VirtualBox\VBoxManage.exe" list vms --long
```
**查看运行着的虚拟机**
```bat
"D:\Program Files\Oracle\VirtualBox\VBoxManage.exe" list runningvms
```
**开启虚拟机在后台运行**
```bat
"D:\Program Files\Oracle\VirtualBox\VBoxManage.exe" startvm <vm_name> -type headless
```
**开启虚拟机并开启远程桌面连接的支持**
```bat
"D:\Program Files\Oracle\VirtualBox\VBoxManage.exe" startvm <vm_name> -type vrdp
```
**改变虚拟机的远程连接端口,用于多个vbox虚拟机同时运行**
```bat
"D:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm <vm_name> vrdpprot <ports>
```
关闭虚拟机
```bat
"D:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm <vm_name> acpipowerbutton
```
强制关闭虚拟机
```bat
"D:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm <vm_name> poweroff
```

要后台启动只要在桌面新建一个批处理文件(.bat文件)。 写上一句代码就可以了：
```
"D:\Program Files\Oracle\VirtualBox\VBoxManage.exe" startvm UbuntuServer -type headless
```
把路路径和虚拟机名改成自己的就可以了，以后每次要启动双击文件就可以了。

## 附录

我的个人集群启动脚本：
**startCentosHadoopCluster.bat.bat：**
```bat
@echo off
@rem # 脚本功能: 批量以无界面的方式启动VBox虚拟机
@rem 
@rem # 命令说明:
@rem # 以无界面的方式启动VBox虚拟机: -type headless 
@rem # VBoxManage.exe startvm vmName -type headless 
@rem # or
@rem # VBoxHeadless -startvm vmName #启动后会阻塞控制台


@rem 要运行的虚拟机:CentOS_HadoopSlave2, CentOS_HadoopSlave1, CentOS_HadoopMaster

@rem 在启动虚拟机之前先检查虚拟机是否在运行
set tmpFile="./tmpfile.tmp"
if exist %tmpFile% (del %tmpFile%)

@rem 获取正在运行的虚拟机信息
"C:/Program Files/Oracle/VirtualBox/VBoxManage.exe" list runningvms > %tmpFile%

set exist=0
for /F %%i in ('findstr "CentOS_HadoopSlave2" %tmpFile%') do (set exist=1)
if %exist% == 0 (
    echo "以无界面的方式启动虚拟机: CentOS_HadoopSlave2"
    "C:/Program Files/Oracle/VirtualBox/VBoxManage.exe" startvm CentOS_HadoopSlave2 -type headless 
)^
else (
    echo "CentOS_HadoopSlave2 虚拟机已经在运行"
)

set exist=0
for /F %%i in ('findstr "CentOS_HadoopSlave1" %tmpFile%') do (set exist=1)
if %exist% == 0 (
    echo "以无界面的方式启动虚拟机: CentOS_HadoopSlave1"
    "C:/Program Files/Oracle/VirtualBox/VBoxManage.exe" startvm CentOS_HadoopSlave1 -type headless 
)^
else (
    echo "CentOS_HadoopSlave1 虚拟机已经在运行"
)

set exist=0
for /F %%i in ('findstr "CentOS_HadoopMaster" %tmpFile%') do (set exist=1)
if %exist% == 0 (
    echo "以无界面的方式启动虚拟机: CentOS_HadoopMaster"
    "C:/Program Files/Oracle/VirtualBox/VBoxManage.exe" startvm CentOS_HadoopMaster -type headless 
)^
else (
    echo "CentOS_HadoopMaster 虚拟机已经在运行"
)

del %tmpFile%
@rem 在用户登录时自动执行该脚本时可带'auto_enter'参数
if "%1" neq "auto_enter" (
    pause
)

@echo on
```

**stopCentosHadoopCluster.bat**
```bat
@echo off
@rem # 脚本功能: 批量保存VBox虚拟机状态,并停止虚拟机
@rem 
@rem # 命令说明:
@rem # 将虚拟机的当前状态保存到磁盘并停止虚拟机
@rem # VBoxManage controlvm <vm_name> savestate 
@rem # or
@rem # 关闭虚拟机
@rem # VBoxManage controlvm <vm_name> acpipowerbutton


@rem 要保存并退出的虚拟机:CentOS_HadoopSlave2, CentOS_HadoopSlave1, CentOS_HadoopMaster

@rem 停止虚拟机之前先检查虚拟机是否在运行
set tmpFile="./tmpfile.tmp"
if exist %tmpFile% (del %tmpFile%)

@rem 获取正在运行的虚拟机信息
"C:/Program Files/Oracle/VirtualBox/VBoxManage.exe" list runningvms > %tmpFile%


set running=0
for /F %%i in ('findstr "CentOS_HadoopSlave2" %tmpFile%') do (set running=1)
if %running% == 1 (
    echo "保存虚拟机状态并休眠虚拟机: CentOS_HadoopSlave2"
    "C:/Program Files/Oracle/VirtualBox/VBoxManage.exe" controlvm CentOS_HadoopSlave2 savestate
)^
else (
    echo "CentOS_HadoopSlave2 虚拟机不在运行状态"
)

set running=0
for /F %%i in ('findstr "CentOS_HadoopSlave1" %tmpFile%') do (set running=1)
if %running% == 1 (
    echo "保存虚拟机状态并休眠虚拟机: CentOS_HadoopSlave1"
    "C:/Program Files/Oracle/VirtualBox/VBoxManage.exe" controlvm CentOS_HadoopSlave1 savestate
)^
else (
    echo "CentOS_HadoopSlave1 虚拟机不在运行状态"
)

set running=0
for /F %%i in ('findstr "CentOS_HadoopMaster" %tmpFile%') do (set running=1)
if %running% == 1 (
    echo "保存虚拟机状态并休眠虚拟机: CentOS_HadoopMaster"
    "C:/Program Files/Oracle/VirtualBox/VBoxManage.exe" controlvm CentOS_HadoopMaster savestate
)^
else (
    echo "CentOS_HadoopMaster 虚拟机不在运行状态"
)

del %tmpFile%

@rem 在用户注销时自动执行该脚本时可带'auto_out'参数
if "%1" neq "auto_out" (
    pause
)
@echo on

```