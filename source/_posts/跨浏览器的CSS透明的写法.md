---
title: "跨浏览器的CSS透明的写法"
categories: [ "Css" ]
tags: [ "css","alpha" ]
draft: false
slug: "cross-browser-transparent-css-method"
date: "2012-08-03 15:56:00"
---

selector {
   filter: alpha(opacity=60); /* MSIE/PC */
   -moz-opacity: 0.6; /* Mozilla 1.6 and older */
   opacity: 0.6;
}