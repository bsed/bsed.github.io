---
title: "国内网站常用的一些 CDN 公共库加速服务小结"
categories: [ "Css" ]
tags: [ "cdn","lib" ]
draft: false
slug: "cdn-libs-common"
date: "2014-09-07 12:55:00"
---

> CDN公共库是指将常用的JS库存放在CDN节点，以方便广大开发者直接调用。与将JS库存放在服务器单机上相比，CDN公共库更加稳定、高速。

    一 般的CDN公共库都会包含全球所有最流行的开源JavaScript库，你可以在自己的网页上直接通过script标记引用这些资源。这样做不仅可以为您 节省流量，还能通过CDN加速，获得更快的访问速度。
目前国内的一些比较大的公共CDN服务：
<!--more-->
#### 百度CDN公共库 ####

百度公共CDN为站长的应用程序提供稳定、可靠、高速的服务，包含全球所有最流行的开源JavaScript库。

官网：http://developer.baidu.com/wiki/index.php?title=docs/cplat/libs

Ps：百度的速度目前来说应该是和新浪差不多的。不过jQuery的版本比SAE少几个，其他类库应该都差不多。

#### 新浪云计算CDN公共库 ####

新浪云计算是新浪研发中心下属的部门，主要负责新浪在云计算领域的战略规划，技术研发和平台运营工作。主要产品包括 应用云平台Sina App Engine（简称SAE）。

SAE的CDN节点覆盖全国各大城市的多路（电信、联通、移动、教育）骨干网络，使开发者能够方便的使用高质量的CDN服务。

官网：http://lib.sinaapp.com/

Ps：网上有评测说新浪的速度比百度的要好。个人没感觉出来。亲测半夜的时候出现过几次加载慢的情况（不知道其他站长遇到过没有）。

#### 又拍云JS库CDN服务 ####

又拍云存储是杭州纬聚网络有限公司旗下项目，成立于2005年6月，前期主要为又拍网、又拍图片管家提供图片云存储／云计算服务，于2010年2月对所有用户开放使用。

主要专注于海量小文件的存储与分发及图片云计算领域。提供的两大核心服务：静态文件云存储、CDN加速处理。

官网：http://jscdn.upai.com/

Ps：又拍云js库提供了常用的JavaScript库CDN服务。算是起步较早的cdn加速服务了。速度和稳定性也不错。不过js库有点少，有些前卫的js库可能不提供。

#### 七牛云存储 开放静态文件CDN ####

像 Google Ajax Library，Microsoft ASP.net CDN，SAE，Baidu，Upyun 等 CDN 上都免费提供的 JS 库的存储，但使用起来却都有些局限，因为他们只提供了部分 JS 库。但七牛云存储提供一个尽可能全面收录优秀开源库的仓库，并免费提供 CDN 加速服务。

官网：http://www.staticfile.org/

Ps：同时，开放静态文件CDN也提供开源库源接入的入口，让所有人都可以提交开源库，包括 JS、CSS、image 和 swf 等静态文件。
上面这几个，我个人测试结果:百度云应该是最快的， 又拍的服务太少，几乎不用考虑.阿里云据说也提供了公共CDN服务，但目前官网找不到具体服务页面，暂时不说了

#### 360网站卫士CDN前端公共库 ####

托管在360众多的全国CDN节点上，覆盖电信、联通、移动等主流运营商线路，您可以在自己的网页上直接通过script标记引用这些资源，让网站访问速度瞬间提速！

只需替换一个域名就可以继续使用Google提供的前端公共库和免费字体库,让网站访问速度瞬间提速。

官网：http://libs.useso.com/

Ps：360的步伐现在是越来越快了。各种技术和提供的服务更新的速度是飞快的。360CDN服务也是最近才推出的（貌似主要还是Google被墙了 的原因）。提供了大多数的前端js库，还在自己的服务器上面缓存了Google的前端公共库和免费字体库，这个算是其他国内的cdn公共库没有的。速度和 稳定性也不错，当然相比较而且还是略逊于百度和新浪(亲测有些地方宽带线路会断线，可能也是很少部分吧)。毕竟提供服务还没多久。具体使用方法可查看WordPress利用360CDN公共库解决Google Open Sans字体无法加载.

目前国外的一些比较大的公共CDN服务：

#### CDNJS ####

CDNJS提供非常完整的 JavaScript 程式库，无论是热门或是冷门的一应俱全。若你觉得它们缺少哪些好用的函式库，也可以自行提交到网站里，通过审核后就 CDNJS 就会为你分流 js文件！这项服务是结合 CloudFlare、Pingdom 与 S3Stat的，稳定性与速度自然不在话下。CDNJS提供的 JavaScript Libraries 全部列在网站首页，使用者可以直接搜索。这些程式库都有标示版本编号、标签以及原维护网站链结。

官网：http://www.cdnjs.com/

Ps：CDNJS应该算是最完整的的JS库了。存储了大部分主流的 JS 库，甚至 CSS、image 和 swf，不过很多国内优秀开源库是没有的。很多国外前卫的Js库在CDNJS大都能找到。国内的速度虽然比不上其他的几个国内的CDN服务，但是相对来说 其实还可以。

当然你也可以使用国人提供的CDNJS国内镜像网站的又拍云路径来引用相关JS和CSS文件。

国内镜像：http://www.cdnjs.cn/

CDNJS国内镜像托管在又拍云存储，但是各种JS或者CSS类库比又拍云自己出的JS库丰富很多,而且每天同步更新且支持https协议。

#### Google Hosted Libraries ####

Google出品，必属精品了。虽然最近Google全线产品被墙了，连基本的Google搜索服务都无法使用了。但是谷歌的公共CDN公共库应该是 最强大的了，像其中的前卫的各种代码类库和Google Web Font 字体库，国内几大公共CDN服务几乎都不提供支持。

官网：https://developers.google.com/speed/libraries/

Ps：当然， Google打不开怎么办？除了使用国内的cdn库，也没有什么好办法了。如：国内cdn不提供的js库使用七牛云存储cdn加速服务、Google Fonts Open Sans字体库使用360CDN公共库代替等。

#### Microsoft ASP.net CDN ###

ASP.NET开发团队推出的一个新的微软Ajax CDN（Content Delivery Network，内容分发网络）服务，该服务提供了对AJAX库（包括jQuery 和 ASP.NET AJAX）的缓存支持。该服务是免费的，不需任何注册，可用于商业性或非商业性用途。

官网：http://www.asp.net/ajaxlibrary/cdn.ashx

Ps：微软出品，自然不会太差。虽然在天朝，速度依然不会太慢（当然比不上国内的其他cdn）。

#### jsDelivr ####

MaxCDN是一家价格相对比较便宜的CDN公司，在全球分布着众多的节点。 jsDelivr是基于MaxCDN的一个免费开源的 CDN 解决方案，用于帮助开发者和站长。jsDelivr包含 JavaScript 库、jQuery 插件、CSS 框架、字体等等 Web 上常用的静态资源。

官网：http://www.jsdelivr.com/

Ps：每一款CDN的节点数量都是大家所关心的，jsDelivr总共提供着13个节点。加载速度和CDNJS基本差不多，国内用户建议使用国内CDN服务最佳。大家可以自己测试看看。

`总结`：这些CDN公共库大都各具特色。大家可以自己选择性去使用。速度和稳定性以国内的百度和新浪为最佳（当然这是个人意见）。鉴于Google已经被墙，所有关于Google的服务大家还是尽快转移阵地，使用国内的CDN公共库服务吧。

*国外的：*

速度测试：http://www.cdnperf.com/

#### cloudflare ####
https://www.cloudflare.com/
http://www.cedexis.com/