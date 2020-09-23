---
title: 小新Pro黑苹果OC6.1 引导开机自动选择
date: 2020-09-08 09:12:00
updated: 2020-09-08 09:15:22
tags: 
- sketch-connect
categories: 
- sketch

---
小新Pro13安装了黑苹果和OC引导，修改默认启动项，开机不用按键，自动选择进入系统。

需要用到的软件
OpenCoreConfiguator（OCC）
下载链接：[https://macwk.com/soft/opencore-configurator](https://macwk.com/soft/opencore-configurator)

圈外网速好的话，也可以访问官网。


<!--more-->


打开OpenCoreConfiguator这个软件，点击屏幕上方【tool】，然后【mount】。
![xiaoxinpro_oc_01.png][1]

点击【Mount EFI】，然后【Open Partition】。
![xiaoxinpro_oc_02.png][2]

打开 EFI文件夹，然后 打开 OC，打开【config.plist】

右键，【打开方式】，选择【ProperTree.app】
![xiaoxinpro13_oc_03.png][3]

打开 ProperTree.app 这个软件后，找到 `Misc/Boot/Timeout`, 如下图打开。建议Timeout改为3，如果觉得太慢了也可以改为1，不建议改为0 (笔者截图修改的为`1`)。
![xiaoxinpro_0c_04.png][4]


把`Misc/Security/AllowSetDefault`的值设置为true，如下图，勾选即可。
![iShot2020-09-08 09.10.31.png][5]


重启电脑，在OC引导选择界面中，键盘上下方向键光标选择默认启动项，按`crtl+enter` 即可。倒计时结束后就会自动进入系统。
![xiaoxinpro_oc_06.png][6]


  [1]: https://imgs.gnux.cn/usr/uploads/2020/09/1634878109.png
  [2]: https://imgs.gnux.cn/usr/uploads/2020/09/811218013.png
  [3]: https://imgs.gnux.cn/usr/uploads/2020/09/4286338370.png
  [4]: https://imgs.gnux.cn/usr/uploads/2020/09/2118888235.png
  [5]: https://imgs.gnux.cn/usr/uploads/2020/09/2360941322.png
  [6]: https://imgs.gnux.cn/usr/uploads/2020/09/481872085.png