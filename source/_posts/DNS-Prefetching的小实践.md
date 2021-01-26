---
title: "DNS Prefetching的小实践"
categories: [ "Css" ]
tags: [ "dns","html","prefetch" ]
draft: false
slug: "dns-prefetching-little-practice"
date: "2015-03-31 20:48:00"
---

在实践之前，我们先介绍一下“资源预拉取”的性能优化的技术，这是一种通过预拉取可以告诉浏览器用户在未来可能用到哪些资源的技术。

而预取，一共有两部分：一部分是资源的预取；还有一部分是DNS的预解析。

以前这种实践也被称为“prebrowsing”，但这并不是一种单一的技术，实际上可以拆分成很多小点：dns-prefetch, preconnect, prefetch, subresource, prerender等等。

而今天要讲的“DNS prefetching”就是DNS的预解析的一小部分。

## 作用

DNS prefetching通过指定具体的URL来告知客户端未来会用到相关的资源，告诉客户端可以尽早的解析DNS。

比如我们需要让example.com的静态资源提前加载域名解析，在<head>就可以这么写：


<!--more-->

```html
<link rel="dns-prefetch" href="//example.com">
```
当请求这个域名下的文件时就不需要等待DNS查询了。

## 用法

如chromium的官方文档所说，chrome 会自动把当前页面的所有带href的link的dns都prefetch一遍：

**Manual Prefetch**

> Chromium uses the "href" attribute of hyperlinks to find host names to
> prefetch. However, some of those hyperlinks may be redirects, for
> example if the site is trying to count how many times the link is
> clicked. In those situations, the "true" targeted domain is not
> necessarily discernible by examining the content of a web page, and so
> Chromium not able to prefetch the final targeted domain.

再根据官方文档发现，a标签的默认启动在HTTPS不起作用：

**DNS Prefetch Control**

> By default, Chromium does not prefetch host names in hyperlinks that
> appear in HTTPS pages. This restriction helps prevent an eavesdropper
> from inferring the host names of hyperlinks that appear in HTTPS pages
> based on DNS prefetch traffic. The one exception is that Chromium may
> periodically re-resolve the domain of the HTTPS page itself.

在<head>就加上下面的一句代码，就可以强制启动：
```html
<meta http-equiv="x-dns-prefetch-control" content="on">
```
## 效果

合理的`dns prefetching`能对页面性能带来`50ms ~ 300ms`的提升，有人做了这方面的统计。

再如chromium的官方文档所说，dns prefetching的网络消耗是极低极低的：

> Each request typically involves sending a single UDP packet that is
> under 100 bytes out, and getting back a response that is around 100
> bytes. This minimal impact on network usage is compensated by a
> significant improvement in user experience.

## 实践

在上面的代码中再添加一句，如下
```html
<link rel="dns-prefetch" href="//secure.gravatar.com" />
```
## 参考

国内的

www.alloyteam.com/2015/10/prefetching-preloading-prebrowsing/
segmentfault.com/a/1190000003944417
zhuanlan.zhihu.com/p/22362198?refer=jscss

国外的

patrickhamann.com/workshops/performance/tasks/2_Critical_Path/2_3.html
calendar.perfplanet.com/2012/speed-up-your-site-using-prefetching/
csswizardry.com/2013/01/front-end-performance-for-web-designers-and-front-end-developers/#section:dns-prefetching
medium.com/@luisvieira_gmr/html5-prefetch-1e54f6dda15d#.6tv1ne3rn
www.bramstein.com/writing/preload-hints-for-web-fonts.html
blog.secure64.com/?p=367
