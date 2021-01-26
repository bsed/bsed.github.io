---
title: "Ubuntu 15 开机时显示错误：nouveau 错误    "
categories: [ "Linux" ]
tags: [ "ubuntu","nouveau" ]
draft: false
slug: "ubuntu-15-boot-display-error-nouveau-error"
date: "2015-10-24 21:03:00"
---

![yigrherb_Ubuntu15_nouveau  .jpg][1]
http://forum.ubuntu.org.cn/viewtopic.php?t=469729

我是遇到系统提示“ACPI PCC probe failed”这问题百度进来的这贴的，试了一下4楼的方法和更新内核都无效，重装Ubuntu Linux 15.04后提示没有了，之后在装NVIDIA专有驱动后又出现提示，怀疑是显卡驱动问题，果然装回nouveau开源驱动提示就没了。继续百度，有帖子说是开源驱动没屏蔽造成的，屏蔽nouveau后，装回专有驱动还是有提示，最后干脆卸载了nouveau开源驱动，重启提示终于没了，大家可以试试这方法～～

系统版本：Ubuntu Linux 15.04

系统提示：ACPI PCC probe failed...starting version XXX...

**解决方法：**

1、无视之，百度有帖子说ACPI PCC probe failed不影响使用正常使用，就是开机时出个提示。

2、如果你觉得这样开机慢，可以先试试4L的方法和更新内核。

3、不行就试试屏蔽nouveau（sudo gedit /etc/modprobe.d/blacklist.conf -> 追加 “blacklist nouveau”，注意：没gedit可以用vi）

4、仍然无效就卸载nouveau（`sudo apt-get purge xserver-xorg-video-nouveau`）

5、还不行就继续百度咯，加油！ 安装最新的英伟达驱动（我没试过）


<!--more-->


 
**【我遇到的现象】**

我的Ubuntu 15 安装了NVIDIA专有驱动后，也出现了“ACPI PCC probe failed”错误，但是不影响进入系统。好吧，根据上述帖子的经验，我决定屏蔽 开源显卡驱动nouveau，最后问题解决，不再提示那错误！

2.7843531 Error parsing PCC subspaces from PCCT
2.7843781 ACPI PCC probe failed


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/561027592.jpg