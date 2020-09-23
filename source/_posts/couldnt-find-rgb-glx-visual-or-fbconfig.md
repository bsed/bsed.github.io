---
title: Ubuntu 18.04 couldn't find RGB GLX visual or fbconfig
date: 2018-03-11 21:07:00
updated: 2018-03-11 21:07:08
tags: 
- css
- px
- em
categories: 
- css

---
在Ubuntu系统下运行glxinfo命令时，出现错误：
couldn't find RGB GLX visual or fbconfig
 
可以通过以下命令进行解决：


<!--more-->


```bash
sudo apt-get remove --purge xserver-xorg
sudo apt-get install xserver-xorg
sudo dpkg-reconfigure xserver-xorg
sudo reboot
```
OK，运行以上命令后问题得到了解决。