---
title: "利用timthumb缓存图片，加速网页载入"
categories: [ "PHP" ]
tags: [ "timthumb" ]
draft: false
slug: "using-timthumb-to-cache-the-image-accelerate-the-webpage-load"
date: "2012-03-02 09:56:00"
---

首页利用了timthumb.php来实现缩略图和图片缓存的功能。引用timthumb官方介绍：是一款小型的图片缩放、裁剪和缓存的PHP脚本。具体的说明可以查阅其google code官网：[http://code.google.com/p/timthumb/](http://code.google.com/p/timthumb/) ，目前最新版本为2.8.10

timthumb.php有多种用法，这里我就只举一个常用的例子来说明timthumb.php的用法。
## 下载与部署 ##

此为官方源码：http://timthumb.googlecode.com/svn/trunk/timthumb.php


<!--more-->


将下载好的timthumb.php放置于网站根目录（或者主题目录），等待调用

## 调用方法 ##

`<img src="网站路径/timthumb.php?src=图片地址&h=100&w=100&q=100"/>`

上述调用方法一目了然，需要注意的是后面的三个常用参数

h:高度height w:宽度width q:质量quality

这三个参数可同时使用，也可以单独使用，例如
`<img src="网站路径/timthumb.php?src=图片地址&h=100&q=80"/>`
上述代码表示图片以100像素的高度进行缩放，质量为100（最好）

## 扩展阅读 ##

我们常用wordpress、emlog等程序建立自己的博客，有些模版里都包含有“获取日志第一张图片”的函数

如：wordpress

    //获取日志第一张图片
    function catch_that_image() {
          global $post, $posts;
          $first_img = '';
          ob_start();
          ob_end_clean();
          $output = preg_match_all('/<img.+src=[\'"]([^\'"]+)[\'"].*>/i', $post->post_content, $matches);
          $first_img = $matches [1] [0];
          if(empty($first_img)){ //Defines a default image
            $first_img = "/images/default.jpg";
          }
          return $first_img;
        }

此函数就是获取日志第一张图片的基本函数，我们可以利用它结合timthumb.php来实现文章缩略图，而不是直接调用第一张图片。

调用范例：

    <img src="<?php bloginfo('template_url'); ?>/timthumb.php?src=<?php echo catch_first_image() ?>&h=80&zc=1"/>

上述代码中 <?php bloginfo(‘template_url’); ?> 在wordpress中表示取模版绝对路径。

又如：emlog
  //获取日志第一张图片

    function getThumbnail($blog_id){
        $db = MySql::getInstance();
        $sql = "SELECT * FROM ".DB_PREFIX."attachment WHERE blogid=".$blog_id." AND (`filepath` LIKE '%jpg' OR `filepath` LIKE '%gif' OR `filepath` LIKE '%png') ORDER BY `aid` ASC LIMIT 0,1";
        //die($sql);
        $imgs = $db->query($sql);
        $img_path = "";
        while($row = $db->fetch_array($imgs)){
             $img_path .= BLOG_URL.substr($row['filepath'],3,strlen($row['filepath']));
        }
        return $img_path;
    }

emlog和wodpress用法一致，在此就不详述了。
## 缓存功能 

timthumb在默认设置下会在同级目录下的cache（如没有此文件夹，程序会自己建立）中写入*.txt的缓存文件，当用户第二次访问某个已被缓存的图片时就会先自动从缓存文件夹中读取txt缓存。

直到以
`<img src="图片真实地址"/>`
这样的形式访问时才会读取图片，这样做的目的很简单，就是加速访问。