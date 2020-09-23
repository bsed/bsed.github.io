---
title: Typecho自定义分页样式
date: 2016-12-19 17:05:00
updated: 2019-12-13 17:07:44
tags: 
- fonts
- vscode
- fira-code
categories: 
- js

---
增加更加多样的分页输出样式选项，包括:

- wrapTag 外层包裹标签名，默认ol
- wrapClass 外层包裹类名
- itemTag 内层标签名， 默认li
- textTag 直接输出文字的标签名
- currentClass 当前聚焦类名
- prevClass 上一页类名
- nextClass 下一页类名


<!--more-->


**方法一** ：
```php
<?php $this->pageNav('«', '»', 3, '...', array('wrapTag' => 'ol', 'wrapClass' => 'page-navigator', 'itemTag' => 'li', 'textTag' => 'span', 'currentClass' => 'current', 'prevClass' => 'prev', 'nextClass' => 'next')); ?>
```
**方法二**：


```php
<?php $this->pageNav('«', '»', 3, '...', 'wrapTag=ol&wrapClass=page-navigator&itemTag=li&textTag=span&currentClass=current&prevClass=prev&nextClass=next'); ?>
```