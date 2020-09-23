---
title: 如何在Typecho 添加 FacyBox灯箱
date: 2016-10-26 16:52:00
updated: 2019-10-25 16:52:55
tags: 
- typecho
- php
- facybox
categories: 
- php

---
## 引用 FancyBox插件

把下面内容添加到 `header.php` 中 `</head>` 前面

```html
<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.7/jquery.min.js"></script> <!--如果主题已经引用了jQuery库，可以忽略这条-->
<link rel="stylesheet" href="https://cdn.staticfile.org/fancybox/3.5.2/jquery.fancybox.min.css">
<script src="https://cdn.staticfile.org/fancybox/3.5.2/jquery.fancybox.min.js"></script>
```

## 修改post.php

打开post.php，将

```
<?php $this->content(); ?>
```

修改成

```php
<?php
    $pattern = '/\<img.*?src\=\"(.*?)\"[^>]*>/i';
    $replacement = '<a href="$1" data-fancybox="gallery" /><img src="$1" alt="'.$this->title.'" title="点击放大图片"></a>';
    $content = preg_replace($pattern, $replacement, $this->content);
    echo $content;
?>
```

## FancyBox 初始化

把下面js添加到 `footer.php` 文件的</body>前

```html
<script type="text/javascript">
    $(document).ready(function () {
        $( ".fancybox").fancybox();
    });
</script>
```