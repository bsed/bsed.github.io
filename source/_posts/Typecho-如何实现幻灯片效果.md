---
title: "Typecho 如何实现幻灯片效果"
categories: [ "PHP" ]
tags: [ "typecho","幻灯片" ]
draft: false
slug: "how-to-achieve-the-effect-of-slideshow-by-typecho"
date: "2015-10-08 16:59:00"
---

## 引用swiper插件
**Swiper**是目前应用较广泛的网页触摸内容滑动js插件（幻灯片插件）。

### 1、在header.php 中 添加CSS引用（加在</head>前面）
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/Swiper/4.0.2/css/swiper.min.css">
```


<!--more-->


## 2、在footer.php 中引用js（加在</body>前面）
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/Swiper/4.0.2/js/swiper.min.js"></script>
<script>        
  var mySwiper = new Swiper ('.swiper-container', {
    pagination: {
      el: '.swiper-pagination',
    },
    navigation: {
      nextEl: '.swiper-button-next',
      prevEl: '.swiper-button-prev',
    },
  })        
</script>
```
## 3、改造post.php
找到
```php
<?php $this->content(); ?>
```
替换成

```php
<?php
$content = preg_replace('/\<img.*?src\=\"(.*?)\"[^>]*>/i', '', $this->content);
$content = preg_replace('/\<br>/i', '', $content);
echo $content;
?>
<div class="swiper-container">
  <div class="swiper-wrapper">
   <?php
   $fullcontent = $this->content;
   $reg = '/(http:|https:)(.*?)(.jpg|.png|.gif|.jpeg)/i';
   $matches = array();
   preg_match_all($reg, $fullcontent, $matches);
   $countPostImg = count($matches[0]);
   for ($k=0; $k < $countPostImg; $k++) { 
   echo ' <div class="swiper-slide"><img itemprop="screenshot" src="'.$matches[0][$k].'" alt="'.$this->title.'"/></div>';}
   ?>
 </div>  
 <div class="swiper-pagination"></div>
 <div class="swiper-button-prev"></div>
 <div class="swiper-button-next"></div>
 </div> 
```
## 4、美化样式
```css
.swiper-container {
    width: 600px;
    height: 300px;
} 
```


![珠江](https://i.loli.net/2018/09/01/5b8a59551a4d8.jpg)|![珠江](https://i.loli.net/2018/09/01/5b8a6ab761262.jpg)|![某收门票公园](https://i.loli.net/2018/09/01/5b8a5994b6e28.jpg)
- | - | -
![某收门票公园](https://i.loli.net/2018/09/01/5b8a5994b6e28.jpg)|![某收门票公园](https://i.loli.net/2018/09/01/5b8a5c8c34439.jpg)|![珠海](https://i.loli.net/2018/09/01/5b8a59d5c50f3.jpg)