---
title: "Removing Gaps Under Images In CSS"
categories: [ "Css" ]
tags: [ "css","img" ]
draft: false
slug: "removing-gaps-under-images-in-css"
date: "2015-04-04 21:21:00"
---

CSS veterans will probably already know this, but here’s a fix to a problem which has been bugging me for ages.

Problem:
<!--more-->
If you have something like:
```
<div>
    <img src="blah.jpg" />
</div>
```
You may see a gap below the image on some browsers, a gap which cannot be removed by setting the padding/margins to 0:
![gap.gif][1]
Solution:

<img> tags are rendered using display: inline by default, which means they act and flow like text does on a page. In order to stop spaces being added (caused by spaces next to the img tag in your html), you should set your img to use display: block:

`<img style="display: block" src="..." alt="" />`
This means your image will no longer act like flowing text and will no longer have a gap below it!

Caveats:

Changing from display: inline means your img tag will ignore things like text-align: center, and other alignments specific to displaying elements inline.
![mind-the-gap.jpg][2]


  [1]: https://imgs.gnux.cn/usr/uploads/2015/04/1989053236.gif
  [2]: https://imgs.gnux.cn/usr/uploads/2015/04/2813962104.jpg