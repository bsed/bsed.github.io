---
title: 利用cloudflare CDN 设置 Page Rules 规则
date: 2019-11-12 13:14:00
updated: 2019-11-12 13:22:15
tags: 
- rime
- 小鹤双拼
categories: 
- linux

---
cloudflare是国外有名的CDN提供商，很多人用，但是很多都不会用，所以经常有人吐槽cloudflare不能怎么缓存html。

![cloudflare_mbioq_01.png][1]

cloudflare提供免费的三个页面页面规则

打开**Page Rules**按照图下设置即可缓存全站内容  速度非常快 演示地址 :kelvin.mbioq.com


<!--more-->


![cloudflare_mbioq_mater.png][2]

如果有后台管理就需要禁止掉后台缓存，我们需要设置一条cloudflare规则 不然可能导致后台无法登陆。

![cloudflare_mbioq.png][3]


  [1]: https://imgs.gnux.cn/usr/uploads/2019/11/2576915593.png
  [2]: https://imgs.gnux.cn/usr/uploads/2019/11/298276169.png
  [3]: https://imgs.gnux.cn/usr/uploads/2019/11/3827401310.png