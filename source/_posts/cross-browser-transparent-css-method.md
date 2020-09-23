---
title: 跨浏览器的CSS透明的写法
date: 2012-08-03 15:56:00
updated: 2015-08-03 15:56:46
tags: 
- css3
categories: 
- css

---
selector {
   filter: alpha(opacity=60); /* MSIE/PC */
   -moz-opacity: 0.6; /* Mozilla 1.6 and older */
   opacity: 0.6;
}