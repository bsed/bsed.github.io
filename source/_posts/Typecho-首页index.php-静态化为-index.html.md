---
title: "Typecho 首页index.php 静态化为 index.html"
categories: [ "PHP" ]
tags: [ "typecho","php","静态化" ]
draft: false
slug: "indexphp-of-typecho-homepage-is-statically-changed-to-indexhtml"
date: "2020-01-05 13:02:00"
---

## 新建文件`cache.php` 上传至网站根目录，cache.php内容如下：
<!--more-->

```php
<?php
$nowtime = time();
$pastsec = $nowtime - $_GET["t"];
if ($pastsec < 600) {
    exit; //10分钟更新一次
}

ob_start(); //打开缓冲区
include("index.php");
$content = ob_get_contents(); //得到缓冲区的内容
$content .= "\n<script language=javascript src=\"chache.php?t=" . $nowtime . "\"></script>"; //加上调用更新程序的代码
file_put_contents("index.html", $content);
if (!function_exists("file_put_contents")) {
    function file_put_contents($fn, $fs) {
        $fp = fopen($fn, "w+");
        fputs($fp, $fs);
        fclose($fp);
    }
}
?>
```
## 访问域名/cache.php
此时会在根目录生成`index.html`查看首页代码，末尾出现`<script language=javascript src="chache.php?t=1578200185"></script>`

说明已经成功生成静态首页。

最终效果如图所示：
![mbioq_typecho_indexphp_to_indexhtml.png][1]


  [1]: https://imgs.gnux.cn/usr/uploads/2020/01/2946829433.png