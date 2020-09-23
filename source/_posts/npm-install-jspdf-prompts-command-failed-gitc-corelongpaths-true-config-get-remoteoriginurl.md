---
title: npm install jspdf 提示 Command failed: git -c core.longpaths=true config --get remote.origin.url
date: 2019-10-12 11:21:00
updated: 2019-10-15 09:25:31
tags: 
- docker
categories: 
- linux

---
如图所示：
![html2canvas_jspdf.png][2]

**错误**是因为国内网络无法透过git://方式将package 下载下来

更新版本：
```bash
npm install -g npm
```


<!--more-->


清除缓存：
```bash
npm cache clear --force
```
更改Download方式：
```bash
git config --global url."https://".insteadOf git://
```
再次执行下面命令成功了。
```bash
npm install --save html2canvas jspdf                                                                                                                                                                                                          + html2canvas@1.0.0-rc.5                                                                                                                                                                                                                                                        + jspdf@1.5.3                                                                                                                                                                                                                                                                   added 26 packages from 66 contributors and updated 18 packages in 19.633s   
```


  [1]: https://imgs.gnux.cn/usr/uploads/2019/10/2750190267.jpeg
  [2]: https://imgs.gnux.cn/usr/uploads/2019/10/792416660.png