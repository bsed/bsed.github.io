---
title: API Fetch下使用Referer和Cache[译文]
date: 2017-08-09 04:24:00
updated: 2017-08-09 10:23:21
tags: 
- android
categories: 
- android

---
## [Referrer and cache control APIs for fetch](https://hacks.mozilla.org/2016/03/referrer-and-cache-control-apis-for-fetch/?utm_source=html5weekly&utm_medium=email)
By Ehsan Akhgari

Posted on March 22, 2016 in Featured Article, ServiceWorkers

Around a year ago, we wrote about the new fetch() API.  The WHATWG Fetch API provides a modern way to fetch network resources and gives you fine grained control over the details of the request and response.  If you’re not familiar with the Fetch API, it would be a nice idea to read about it before proceeding.

We have recently implemented a few new additions to the Fetch API, and in this post I will give an overview of them and include examples of how they can help you develop your web applications.

大约在1年以前， 我们写了篇关于`new fetch()`的API文章。*WHATWG*组织的_fetch api_给我们获取网络资源提供了新的方式，同时它让你能够更好的控制*request*和*response*的细节。如果你还不熟悉_fetch api_，赶紧先去了解一下。

我们最近对*Fetch API*添加了一些新的实现，这篇文章就是让你能够大致的了解这些新的API，并且了解到它们对我们项目开发都有哪些帮助。


<!--more-->


### Referrer control APIs

Using fetch(), you can now control the HTTP request referrer and referrer policy. The HTTP Referer [sic] header is a (misspelled!) header that allows a target page to know what source page the user is coming from (for example, by clicking a link on that page). This is useful for example for gathering analytics data about where your web site users are coming from.

The referrer policy is a new W3C specification which we have been implementing in Firefox that allows the page to provide the browser with a policy that lets the page have more control over how the Referer header is set.  There are a few different policy states, each with a specific goal in mind. Here is a summary.

现在你可以在`fetch()`中控制HTTP请求的*referer*以及referer规则。*HTTP Referer*头是一个Http请求的Header头，允许目标页面知道用户是从哪个原始页面来的（比如点击页面的某个链接）。这是获取分析网站用户来源的一个重要的数据。

*referer 规则*是一个新的W3C标准，并且已经被Firefox实现，它允许页面控制如何设置*referer* Head头。现在有几个不同的规则状态，每一个规则对*referer*的表现都有影响， 具体如下： 

- “no-referrer” prevents sending any Referer header.  This can be useful when you want to hide the Referer header for privacy reasons.  For example, some search engines add information about the user’s search phrase among other things to the URL, and they may not want to leak the user’s search phrase to the search result web sites that the user clicks on.  The “no-referrer” referrer policy could be used for that purpose.
- “no-referrer-when-downgrade” is similar to “no-referrer” with the exception that the Referer header is only omitted when navigating from a secure context to a non-secure one.  For instance, with the search engine example above, if your privacy concern is limited to people monitoring the HTTP traffic instead of the target website, you can use the “no-referrer-when-downgrade” policy. In this case, if a search result links to a secure context, the browser would send the Referer header but if the target web site is an insecure HTTP site, the browser will refuse the send the Referer header in clear text.  This is the default policy if an explicit policy has not been specified.
- “origin-only” will make the browser only include the referring origin and not the full URL in the Referer header.  For example, if you want the target web sites to be able to tell that the user is coming from your search results page without revealing the full URL, you can use “origin-only”. In this case, the browser would strip away anything after the domain name in the URL sent in the Referer header.
  “origin-when-cross-origin” is similar to “origin-only” except that it will only strip out the full URL when navigating across origins.  For example, suppose you want to only include the origin for your search result pages (which we assume to be cross origin if your site is doing a normal web search), but send the full referrer to your own internal pages. This lets your own analytics software know how your users navigate across the pages of your site. In this case, “origin-when-cross-origin” is the right policy to choose.
- “unsafe-url” causes the browser to send the full URL (sans any associated user name, password or fragment) to all pages that the user navigates to, no matter whether they’re cross origin and/or secure.  The real reason why this is called unsafe is that this will reveal the full URL to any target web page, which raises privacy concerns such as those the examples above try to address.  You should consider using a different referrer policy if possible.

- "no-referrer"禁止发送Referer头。当你有控制隐私需要隐藏*referer*头时，这非常有用。举个例子，有些搜索引擎会在请求的URL里面添加用户搜索字段和其他内容，但是他们并不想向搜索结果页面泄露用户的搜索信息。"no-referrer"规则可以运用到这个场景中。
- "no-referrer-when-downgrade"和"no-referrer"非常类似，只有在浏览器从一个安全的网站到一个不安全的网站是，该*refer*头才会被忽略。举例：在上一个搜索引擎的例子中，如果你的隐私限制只是对网络通信进行监控的网站，而不是所有的网站时，你就可以使用"no-referrer-when-downgrade"规则。如果请求转向安全的网站，浏览器就会发送*referer*头部信息，但是如果访问的是一个不安全的HTTP网站时，浏览器则拒绝发送*referer*头。在没有显示的指定运用哪种规则时，这就是默认的规则。
- "origin-only"可以让浏览器的*referer*头包含基础域名，而不是完整的URL。举个例子： 你想让目标网站知道请求是从你这个搜索引擎过去的，但是并不需要整个URL链接，你就可以使用"origin-only"。这样，浏览器就可以将除域名以外的其他部分去除作为*referer*的值发送。
- "origin-when-cross-origin"和"origin-only"非常类似，但这种情况只会发生在跨域情况。举例：对于外部网站，你只希望将域名传递过去，但对于内部网站，你希望传递完整的URL。这可以让你的软件去分析用户请求，所以"origin-when-cross-origin"就是应该选择的正确规则。
- "unsafe-url"不考虑是否跨域，会让浏览器发送完整的URL(包括相关的用户名，密码或者其他片段)到用户访问的其他页面上。之所以说这种方式是不安全的，因为它会像任何访问的目标网站泄露完整的URL地址，这有可能会带来安全隐患。

Right now, in Firefox you can use an <meta name=referrer> element on your page to set a global referrer policy for all network requests initiated from the page.  We are also working on implementing the per-element referrer policy attributes that can be useful when you want to use a different referrer policy for a specific element (such as an <img>).  With the new APIs introduced here, you can also control the referrer and the referrer policy for the resources downloaded using fetch().

The following code examples show a few examples of how you can use these new fetch() features.

现在，在*Firefox*中， 你可以在页面中使用`<meta name=referer>`标签对从这个页面开始的所有请求设置全局的*referer*头。我们也在努力的为每个元素添加该*referrer*。同时，这个新的API也可以让你在使用`fetch`时控制*referrer*和*referrer规则*。

```
  // Let’s assume that the code below runs on https://example.site/page.html
  //假设下面的代码运行在 https://example.site/page.html
  // Download a json but don't reveal who is downloading it
  //不泄露referer
  fetch("sneaky.json", {referrerPolicy: "no-referrer"})
    .then(function(response) { /* consume the response */ });

  // Download a json but pretend another page is downloading it
  fetch("sneaky.json", {referrer: "https://example.site/fake.html"})
    .then(function(response) { /* consume the response */ });

  // Download a potentially cross-origin json and don't reveal
  // the full referrer URL across origins
  fetch(jsonURL, {referrerPolicy: "origin-when-cross-origin"})
    .then(function(response) { /* consume the response */ });

  // Download a potentially cross-origin json and reveal a
  // fake referrer URL on your own origin only.
  fetch(jsonURL, {referrer: "https://example.site/fake.html",
                  referrerPolicy: "origin-when-cross-origin"})
    .then(function(response) { /* consume the response */ });

  // Override sending the document global referrer policy set using
  // to send the full referrer URL.
  // Be careful!
  fetch(jsonURL, {referrerPolicy: "unsafe-url"})
    .then(function(response) { /* consume the response */ });
```
If your site uses service workers, then you can examine the referrer and the referrer policy that accompanies a fetched resource. Look inside the fetch event handler using the referrer and referrerPolicy attributes of Request objects.

This API will be available in Firefox 47, which is currently available in the developer edition release channel for testing.

如果你的网站还在使用service worker, 那么你就可以使用`fetch`来测试`referrer`和`referrerPolicy`。使用`Request`对象的`referrer`和`referrerPolicy`属性来观察`fetch`事件处理的细节。

### Fetch cache control APIs

The resources downloaded through fetch(), similar to other resources that the browser downloads, are subject to the HTTP cache.  This is usually fine, since it means that if your browser has a cached copy of the response to the HTTP request. It can use the cached copy instead of wasting time and bandwidth re-downloading from a remote server.

However, there are cases where you would want some control over whether the browser’s HTTP cache is used for a common idiom.

However, there are cases where you would want some control over whether the browser’s HTTP cache is used. You can ensure that you’re getting a fresh response no matter what’s in the browser’s HTTP cache by cache busting the URL of the resources you want to to fetch into the service worker controlled cache. This is typically done by appending a parameter such as 'cache-bust=' + Date.now() to the URL before downloading it, which is quite ugly.  There is now a better way to do this, using the fetch cache control API.

通过`fetch()`下载资源， 类似于浏览器下载其他资源，受到*HTTP cache*的影响。浏览器缓存一份请求资源返回的内容，这是很棒的一件事情。这可以让浏览器省去浪费时间和带宽去再次请求资源。

然而，有些情况下你需要控制是否继续使用HTTP缓存。当你想获取最新的资源时，但又不确定浏览器是否已经缓存目标资源，通常做法是去破坏缓存。而破坏缓存的通常做法是： 会在请求资源后面添加一个随机字符串`'cache-bust=' + Date.now()`。这种做法非常的丑陋，现在可以使用`fetch`的缓存控制API来完成控制。

The idea behind this API is specifying a caching policy for fetch to explicitly indicate how and when the browser HTTP cache should be consulted.  It’s important to have a good understanding of the HTTP caching semantics in order to use these most effectively.  There are many good articles on the web such as this one that describe these semantics in detail.  There are currently five different policies that you can choose from.

- “default” means use the default behavior of browsers when downloading resources.  The browser first looks inside the HTTP cache to see if there is a matching request.  If there is, and it is fresh, it will be returned from fetch().  If it exists but is stale, a conditional request is made to the remote server and if the server indicates that the response has not changed, it will be read from the HTTP cache.  Otherwise it will be downloaded from the network, and the HTTP cache will be updated with the new response.
- “no-store” means bypass the HTTP cache completely.  This will make the browser not look into the HTTP cache on the way to the network, and never store the resulting response in the HTTP cache.  Using this cache mode, fetch() will behave as if no HTTP cache exists.
- “reload” means bypass the HTTP cache on the way to the network, but update it with the newly downloaded response.  This will cause the browser to never look inside the HTTP cache on the way to the network, but update the HTTP cache with the downloaded response. Future requests can use that updated response if appropriate.
- “no-cache” means always validate a response that is in the HTTP cache even if the browser thinks that it’s fresh.  This will cause the browser to look for a matching request in the HTTP cache on the way to the network.  If such a request is found, the browser always creates a conditional request to validate it even if it thinks that the response should be fresh.  If a matching cached entry is not found, a normal request will be made.  After a response has been downloaded, the HTTP cache will always be updated with that response.
- “force-cache” means that the browser will always use a cached response if a matching entry is found in the cache, ignoring the validity of the response.  Thus even if a really old version of the response is found in the cache, it will always be used without validation.  If a matching entry is not found in the cache, the browser will make a normal request, and will update the HTTP cache with the downloaded response.
  Let’s look at a few examples of how you can use these cache modes.

  这个API的背后的思路就是规范缓存策略，让`fetch`显式的指示HTTP缓存应该在何时，如何被访问。理解HTTP缓存的内涵非常的重要。

现在有5种不同的规则： 

- "default" 表示使用默认的浏览器规则来下载资源。浏览器首先会查看一下HTTP缓存，如果有匹配的请求并且资源是有效的， 那么直接返回缓存结果给`fetch()`。如果存在但是已经过期，浏览器会向服务器发送一个验证请求，如果服务器返回资源没有改动，则继续从缓存中获取资源。否则浏览器将发送请求获取资源，然后更新本地缓存。
- "no-cache"表示浏览器不管本地HTTP缓存是否是最新，始终向远端发起验证操作。这会导致浏览器每次在发起请求的时候都会对HTTP缓存的内容进行验证。如果没有命中缓存，就会发起正常的HTTP请求。在资源下载之后，HTTP缓存会得到更新。
- "force-cache"表示浏览器如果命中HTTP缓存，不管HTTP缓存是否有效，强制使用。所以即使真的在HTTP缓存中命中了一个老版本的资源，浏览器也不会发起验证操作。如果没有命中缓存，浏览器会发起一个正常的HTTP请求，并且会更新HTTP缓存。

下面我们来看看该如何用代码来实现： 

```
  // Download a resource with cache busting, to bypass the cache
  // completely.
  fetch("some.json", {cache: "no-store"})
    .then(function(response) { /* consume the response */ });

  // Download a resource with cache busting, but update the HTTP
  // cache with the downloaded resource.
  fetch("some.json", {cache: "reload"})
    .then(function(response) { /* consume the response */ });

  // Download a resource with cache busting when dealing with a
  // properly configured server that will send the correct ETag
  // and Date headers and properly handle If-Modified-Since and
  // If-None-Match request headers, therefore we can rely on the
  // validation to guarantee a fresh response.
  fetch("some.json", {cache: "no-cache"})
    .then(function(response) { /* consume the response */ });

  // Download a resource with economics in mind!  Prefer a cached
  // albeit stale response to conserve as much bandwidth as possible.
  fetch("some.json", {cache: "force-cache"})
    .then(function(response) { /* consume the response */ });
```

This API is planned for release in Firefox 48, and is currently available in Firefox Nightly for testing.

这个API计划在Firefox48中发布，现在可以在Firfox Nightly 中测试。
