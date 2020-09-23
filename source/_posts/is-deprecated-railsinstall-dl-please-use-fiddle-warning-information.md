---
title: RailsInstall 安装后去掉DL is deprecated， please use Fiddle警告信息
date: 2015-10-17 10:38:00
updated: 2016-01-24 08:34:37
tags: 
- elasticsearch
categories: 
- java

---
**问题：**

 搭建完完ruby on rails环境之后发现每次运行命令总会有这样一个Warning：DL is deprecated， please use Fiddle，
例如:
![yigrherb_RailsInstall_01.png][1]
![yigrherb_RailsInstall_02.png][2]
对运行什么的没有影响，只是Dl过时了，可是Ruby大大不管这个问题，可是看着就烦呐~~


<!--more-->


*解决方法（from stackflow）：*
 
找到安装目录C:Ruby21-x64libruby2.1.0下的dl.rb将里面的warn一行注释掉即可
![yigrherb_RailsInstall_03.png][3]
再次运行后，问题解决

![yigrherb_RailsInstall_04.png][4]


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/1049666266.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/01/1329886436.png
  [3]: https://imgs.gnux.cn/usr/uploads/2016/01/2223999886.png
  [4]: https://imgs.gnux.cn/usr/uploads/2016/01/2519596180.png