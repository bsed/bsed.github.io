---
title: "Debian8开机启动服务技巧"
categories: [ "Linux" ]
tags: [ "linux","debian" ]
draft: false
slug: "debian8-boot-service-skills"
date: "2015-10-14 11:33:00"
---

init启动服务`sysv-rc-conf`

```bash
apt-get install sysv-rc-conf
```
在debian8中update-rc.d已经不被推荐使用了。
于此同时，systemctl也开始发挥应有的作用。
在未来的debian版本中，如果需要服务开机启动，只需要输入：

```bash
systemctl enable ***
```
即可使用推荐的配置开启开机启动了。