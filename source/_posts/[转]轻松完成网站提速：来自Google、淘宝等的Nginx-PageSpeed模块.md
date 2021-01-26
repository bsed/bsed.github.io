---
title: "[转]轻松完成网站提速：来自Google、淘宝等的Nginx PageSpeed模块"
categories: [ "Linux" ]
tags: [ "nginx","pagespeed" ]
draft: false
slug: "transfer-easily-complete-the-website-speed-nginx-pagespeed-module-from-google-taobao-etc"
date: "2015-01-13 09:23:00"
---

[mod_pagespeed](https://developers.google.com/speed/pagespeed/ngx)于2010年发布，让网站管理员可以为其Web应用提速，而不需要深度的性能优化造诣。mod_pagespeed最初版本只作为Apache的模块，并不兼容Nginx这个最流行并为许多大型站点所使用的高性能开源网络服务器。如今Nginx的[PageSpeed Beta](https://developers.google.com/speed/pagespeed/)版终于发布，又名`ngx_pagespeed`。
作为Nginx组件，ngx_pagespeed将重写你的网页，让用户以更快的速度进行访问。重写的工作包括压缩图片、缩减CSS和


<!--more-->


JavaScript、扩展缓存时间，同样还包括其它一些最佳实践：

 - 优化缓存——整合应用程序的数据和逻辑
 - 最小化round-trip次数——削减连续的请求/响应周期数
 - 最小化请求开销——削减上传大小
 - 最小化负载大小——削减响应、下载及缓存页面大小
 - 优化浏览器渲染——改善浏览器页面布局
 - 移动方面的优化——优化站点移动网络和设备方面的相关特性

更多详情查看 [Web Performance Best Practices](https://developers.google.com/speed/docs/best-practices/rules_intro) ，同时mod_pagespeed的 [优化过滤器](https://developers.google.com/speed/docs/mod_pagespeed/filters)同样兼容Nginx。
![ngx_pagespeed.png][1]

经过3个月的α测试，ngx_pagespeed的有效性在上千个站点被证明；而今他们释放了ngx_pagespped Beta版，并邀请更多的用户参与测试。
然而这个模块在Nginx上效果究竟如何，下面一览两个在α测试中的用例：
1. CDN供应商MaxCDN最近发布了一篇关于ngx_pagespeed测试的 [博文](http://blog.netdna.com/developer/nginx-performance-tips-with-the-google-pagespeed-team/)：

> 通过PageSpeed，我们将平均网页时间削减1.57秒，将网站跳出率减少1%，退出比例减少2.5%。总而言之，只通过给Nginx配置添加些许语句就提升了额外的性能……我们会继续参与PageSpeed团队一起测试模块，而我们的目的就是让模块更有助于我们的平台。

2. 流行WordPress托管提供商ZippyKid同样是PageSpeed [最早的测试者](https://www.zippykid.com/2013/04/23/partnership-with-google-to-deliver-fast-wordpress-sites/)：

ngx_pagespeed开发者包括了来自**Google、Taobao等公司的工程师**以及一些其他的个人志愿者，现已于 GitHub上开源。
原文链接： [Speed up your sites with PageSpeed for Nginx](http://googledevelopers.blogspot.com/2013/04/speed-up-your-sites-with-pagespeed-for.html) 

  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/846588651.png