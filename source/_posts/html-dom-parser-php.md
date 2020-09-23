---
title: PHP HTML解析工具(html dom parser)
date: 2010-09-15 13:50:00
updated: 2016-09-11 13:55:31
tags: 
- linux
- ubuntu
categories: 
- linux

---
> 项目：[http://sourceforge.net/projects/simplehtmldom/](http://sourceforge.net/projects/simplehtmldom/)

它具有以下几个特点：

 1. 只支援 PHP5 以上.
 2. 可以分析不严谨 (invalid) 的 HTML. 
 3. 支援简单的 CSS Selector. 
 4. 简单的 DOM 操作. 
 5. 会维持 HTML 中的原始格式 .

下面是使用手册上举的几个简单的使用示例。

## 如何读取 HTML 元素
```php
<? 
include('html_dom_parser.php');
$dom = file_get_dom('http://www.baidu.com/');
// 找出所有网页连结 
$result = $dom->find('a'); 
foreach($result as $v) {echo $v->href . '<br>';}
// 找出所有网页图片 
$result = $dom->find('img'); 
foreach($result as $v) {echo $v->src . '<br>';}
// 找出所有网页中所有 id=gbar 的 div 标签 
$result = $dom->find('div#gbar'); 
foreach($result as $v) {echo $v->innertext . '<br>';}
// 找出所有网页中所有 calss=gb1 的 span 标签 
$result = $dom->find('span.gb1'); 
foreach($result as $v) {echo $v->outertext . '<br>';}
// 找出所有网页中所有 align=center 的 'td 标签 
$result = $dom->find('td[align=center]'); 
foreach($result as $v) {echo $v->outertext . '<br>';} 
?>
```

## 如何修改 HTML 元素
```php
<?
include('html_dom_parser.php');
$dom = file_get_dom('http://www.baidu.com/');
// 移除网页中所有图片 
$ret = $dom->find('img');
foreach($ret as $v) {$v->outertext = '';}
// 修改网页中所有 input 标签 
$ret = $dom->find('input');
foreach($ret as $v) {$v->outertext = '[INPUT]';}
// 显示修改后的网页 
echo $dom->save();
?>
```

## Slashdot网站内容抓取
```php
// Create DOM from URL
$html = file_get_html('http://slashdot.org/');
// Find all article blocks
foreach($html->find('div.article') as $article) {
    $item['title']     = $article->find('div.title', 0)->plaintext;
    $item['intro']    = $article->find('div.intro', 0)->plaintext;
    $item['details'] = $article->find('div.details', 0)->plaintext;
    $articles[] = $item;
}
print_r($articles);
```