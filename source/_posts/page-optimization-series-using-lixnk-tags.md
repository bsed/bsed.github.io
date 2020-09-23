---
title: 页面优化系列之 使用link 标签
date: 2016-10-06 09:00:00
updated: 2016-10-07 14:46:53
tags: 
- javascript
- jstips
categories: 
- js

---
![html-resource-hints-speed-up-websites.webp][1]

> *编者按*:这篇文章是我们的代码优化系列的一部分,我们看看如何优化编码更好的效率,成为更好的程序员。

“预见”浏览器高速上网的未来，给我们带来我们想要的资源之前，我们知道我们想要他们。今天的浏览器已经做了一些预测，然后加快抓取和渲染的文件。要采取这一步到下一步，我们希望除Web开发人员以外做一些其他的工作。

开发人员很好的了解他们的网站导航,和哪些资源请求最常见,因此,他们可以预测未来一些操作。现在需要的是为开发人员找到一个方法来传递这些预测并浏览器和好好利用它们。这就是一些特殊的“HTML links”进来。

## HTTP请求复用

看看这些链接之前,假设一个叫乔想要访问一个网站,通过典型的 `HTTP-requested file-fetching` 刷新网站的请求。

接下来会发生什么:

 - 乔在浏览器的地址栏输入网址,按下“Enter”。
 - 一旦收到这个地址,浏览器会问一个[DNS](https://en.wikipedia.org/wiki/Domain_Name_System)服务商的询问这个网站的IP地址([ISP](https://en.wikipedia.org/wiki/ISP))，并将网站的IP地址返回给乔。
 - DNS服务器的要求。
 - 现在，浏览器知道IP地址，它发送一个消息（TCP协议）的网站服务器请求建立连接。
 - 如果服务器在线的话,它将发送一个回复,确认浏览器浏览器的请求和响应,并确认服务器的消息。(注意:是的,这是一个极其淡化了版本的[TCP客户端和服务器之间握手](https://en.wikipedia.org/wiki/Handshaking#TCP_three-way_handshake)。)
 - 握手时,两者之间建立连接。
 - 现在,浏览器将TCP协议请求转换成HTTP协议请求和请求网站的服务。
 - 服务器,将网站的主页内容返回,由浏览器和等待收到消息的乔非常耐心地等待在电脑面前。

一个典型的HTTP请求经过所有的(以及更多)通过互联网获取文档。如果任何这些过程可以现在在可能的情况下,我们可以减少我们必须等待的时间提供我们想要的资源。

## HTML/LINK

W3C指定 HTML4 link(关系)(rel for relationship) 例如: `dns-prefetch` `preconnect`,`prefetch`,`prerender`。他们一起被称为(W3C)“**资源提示(Resource Hints)**”。现在,我们来看看他们能做什么和他们可以在什么地方使用。


### dns-prefetch

在DNS预取(dns-prefetch),域名解析(即从DNS服务器获得匹配的IP地址)提前完成。

“DNS请求非常小的带宽,但延迟可以相当高,特别是在移动网络。通过大胆的预取DNS结果,可以显著减少延迟在某些时候,比如当用户单击链接。在某些情况下,可以减少延迟。——[Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/HTTP/Controlling_DNS_prefetching)”

我们说有一个参考页面在一个网站有大量的参考链接到它的姐妹网站。当用户访问参考页时，有一个高概率，用户将导航到姐妹网站。所以，对于妹妹网站早期的**DNS轮循**(Loop) 可以减少它需要打开网站的时间（从而提高用户体验）。

通过**DNS预取**可以通过添加此代码到参考页以便于减少延迟。

```html
<link rel="dns-prefetch" href="//mysistersite.org">
```

当一个浏览器进程解析到参考页面的代码时，它会添加的姐妹站点任务队列的DNS查找，当它是免费的从队列中的其他高优先级的任务，它将开始的姊妹网站的DNS解析。

最后当用户点击一个链接,需要的姐妹站点,该站点的DNS解析可能已经完成,和浏览器可以马上开始建立与姐妹站点服务器客户端-服务器的TCP连接,使页面加载速度更快。

这个特性可以在几乎[所有现代浏览器（除了Safari）](http://caniuse.com/#feat=link-rel-dns-prefetch)可用截至2016年3月。

### Preconnect

Preconnect进一步从DNS预取,它建立了一个连接到服务器,可能有一个请求发送后在未来。

“Preconnect是一个重要的工具在你的优化工具箱…它可以消除许多昂贵的往返的从你的请求路径——在某些情况下减少请求延迟由数百甚至数千个毫秒。——[lya Grigorik](https://www.igvita.com/2015/08/17/eliminating-roundtrips-with-preconnect/)”


W3C的理想用例列表preconnect:**重定向(redirects)**。开发人员使用重定向的原因。

在这种情况下,浏览器的下一个请求(重定向站点)是可预测的**100%**,并且**可以preconnected**,**减少导航延迟**。

想象有一个中介网站页面重定向到“xyzsite”,下面的HTML链接将使浏览器preconnect xyzsite服务器,当它到达中介页面。

```html
<link rel="preconnect" href="//xyzsite.com">
```

截至2016年3月,特性可以在Chrome,Opera和[Firefox](https://bugzilla.mozilla.org/show_bug.cgi?id=1135160)上面[使用](https://www.chromestatus.com/feature/5560623895150592)。

### Prefetch

预取(prefetch)，是一种资源，浏览器启动实施资源的域名的DNS解析，然后执行与资源服务器的TCP连接，使得HTTP请求，并最终会在浏览器的缓存预取资源存储。

如果你确定哪些资源需要后，这是事先预取的资源；这就是抓住。预取需要的猜测，如果你猜错了，你可能真的会慢下来，而不是加速你的网站。

“这种技术有可能加快许多互动网站，但不会到处工作。对于一些网站，它只是很难猜测用户下一步可能做什么。对于其他人来说，数据可能会变得陈旧，如果它太快了。要小心不要太快取文件也是很重要的，或者你可以放慢网页的用户已经看。- [Google Developers](https://developers.google.com/speed/articles/prefetching)“

在线书籍、画廊、或投资组合,如果用户最有可能浏览到下一页,预取资源,如图片,可以显著加快速度。这里的代码。
对于网上的书籍，画廊，或投资组合，如果用户最有可能浏览到下一页，预取的资源，如图像，可以加快东西显示。下面是示例代码。

```html
<link rel="prefetch" href="//xyzsite.com/nextimage.jpg">
```

预取 [支持Chrome,Firefox和Opera](http://caniuse.com/#feat=link-rel-prefetch)等浏览器。

### Prerender

只对HTML页面可以做预呈现(prerendering)。预渲染(prerendered)的HTML页面呈现离线，并显示屏幕时，它实际上是用户所需要的。渲染成本较高的计算工作和内存资源，加上，为了使一个页面，浏览器可能需要额外的资源（如图像添加到页面）将导致更多的后续请求的浏览器。
所以,prerender必须小心使用,而不是滥用。添加下面的代码将prerender事先添加到“关于我”的页面。
```html
<link rel="prerender" href="//example.com/about.html">
```

“PreRender暗示可以表明下一个可能的HTML导航目标应用程序：用户代理将取过程中指定的资源作为一个HTML响应。取适当的请求头其他内容类型，或者HTML预处理是不需要的，应用程序可以使用预取的提示。–[W3C](https://www.w3.org/TR/resource-hints/#h-note2)”

截至2016年3月，Prerender已经在[Chrome,IE和Opera上](http://caniuse.com/#feat=link-rel-prerender)可用。

## 注意事项

(1)没有上述资源暗示担保的执行和完成不同阶段的请求,因为当浏览器已经忙着处理请求所需的当前页面用户的操作,执行这些优化会阻碍用户的当前任务。

所以,一切都是排队等待,当浏览器感觉释放足够的执行。

这些资源提示不一定必须出现在页面的加载之前的页面。他们可以通过JavaScript添加后,资源暗示将一如既往地做好自己的本职工作。

(2)W3C HTML链接属性指定了一个名为提示概率,pr值(值0到1)对这些资源的提示,这可以用来提供请求的概率将在未来。我还没有看到这个属性被任何浏览器实现。作为一个例子,下面的代码,有一个80%机会xyzsite将在未来,要求30%的页面。

```html
<link rel="preconnect" href="//xyzsite.com" pr="0.8">
<link rel="prerender" href="//example.com/about.html" pr="0.3">
```

我们也可以添加可选的[crossorigin](https://html.spec.whatwg.org/multipage/infrastructure.html#cors-settings-attributes)属性的资源提示告知浏览器请求的*CORS*凭证有关。

  [1]: https://imgs.gnux.cn/usr/uploads/2016/10/2116456810.webp