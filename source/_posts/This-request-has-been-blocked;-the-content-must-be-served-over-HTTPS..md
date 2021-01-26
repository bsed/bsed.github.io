---
title: "This request has been blocked; the content must be served over HTTPS."
categories: [ "JS" ]
tags: [ "nginx","upgrade-insecure-requests" ]
draft: false
slug: "this-request-has-been-blocked-the-content-must-be-served-over-https"
date: "2020-09-26 10:06:00"
---

```
chunk-libs.415d1108.js:59 Mixed Content: The page at 'https://bkb.gnux.cn/#/login?redirect=%2Fdashboard' was loaded over HTTPS, but requested an insecure XMLHttpRequest endpoint 'http://bkb.gnux.cn/api/admin-user-no-login/login'. This request has been blocked; the content must be served over HTTPS.
(anonymous) @ chunk-libs.415d1108.js:59
t.exports @ chunk-libs.415d1108.js:59
t.exports @ chunk-libs.415d1108.js:26
Promise.then (async)
c.request @ chunk-libs.415d1108.js:8
(anonymous) @ chunk-libs.415d1108.js:8
a @ app.1d982865.js:1
handleLogin @ chunk-6e636bb6.d14ac312.js:1
click @ chunk-6e636bb6.d14ac312.js:1
ne @ chunk-libs.415d1108.js:18
n @ chunk-libs.415d1108.js:18
Zo.i._wrapper @ chunk-libs.415d1108.js:18
/#/login?redirect=%2Fdashboard:1 [Intervention] Slow network is detected. See https://www.chromestatus.com/feature/5636954674692096 for more details. Fallback font will be used while loading: https://bkb.gnux.cn/static/fonts/element-icons.535877f5.woff
app.1d982865.js:1 errError: Network Error
chunk-libs.415d1108.js:18 Uncaught (in promise) Error: Network Error
    at t.exports (chunk-libs.415d1108.js:18)
    at XMLHttpRequest.p.onerror (chunk-libs.415d1108.js:59)
```


<!--more-->


**解决办法** ：

在html头加`<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">`问题解决

