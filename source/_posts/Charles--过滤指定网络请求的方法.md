---
title: "Charles  过滤指定网络请求的方法"
categories: [ "Linux" ]
tags: [ "charles" ]
draft: false
slug: "method-of-charles-filtering-specified-requests"
date: "2019-03-29 11:28:00"
---

我们在对项目进行接口请求参数测试时，需要对一些网络请求进行过滤，只监控指定服务器的请求。有一下3种办法：

方法一：在主界面中间位置Filter 栏中输入需要过滤出来的关键字。例如我们过滤服务器的地址(host)是：`zhtj.youth.cn/zhtj/` , 那么只需要在 Filter 栏中输入`zhtj.youth.cn/zhtj/` 即可。
![charles_01.png][1]


<!--more-->


方法二：菜单栏选择 “Proxy”->”Recording Settings”，在弹出的窗口中选择 Include 栏，再点击“Add”，在弹出的窗口中添加需要监控的协议，主机地址，端口号等信息。如下图所示：

![charles_02.png][2]
![charles_03.png][3]
方法三：在想过滤的网络请求上右击，选择 “Focus”，之后在 Filter 一栏勾选上 Focussed 一项，如下图所示： 

![charles_04.png][4]
可以临时性、快速地过滤掉一些没有通过关键字的网络请求。


  [1]: https://imgs.gnux.cn/usr/uploads/2019/03/580788843.png
  [2]: https://imgs.gnux.cn/usr/uploads/2019/03/1105330934.png
  [3]: https://imgs.gnux.cn/usr/uploads/2019/03/2129606778.png
  [4]: https://imgs.gnux.cn/usr/uploads/2019/03/2229358537.png