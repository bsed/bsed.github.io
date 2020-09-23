---
title: 移动设备（手机和平板）环境检测的 PHP 类库
date: 2015-01-05 09:59:00
updated: 2017-02-19 10:01:13
tags: 
- golang
- unicode
categories: 
- go

---
`Mobile Detect` 是一个轻量级的开源移动设备（手机和平板）检测的 `PHP Class`，它使用 `User-Agent` 中的字符串，并结合 `HTTP Header`，来检测移动设备环境
这个设备检测的 PHP 类库最强大的地方是，它有一个非常完整的库，可以检测出所用的设备类型（包括操作类型，以及手机品牌等都能检测）和浏览器的详细信息。

Mobile_Detect 简单使用实例`
```php
include 'Mobile_Detect.php';
$detect = new Mobile_Detect();

// Check for any mobile device.
if ($detect->isMobile())

// Check for any tablet.
if($detect->isTablet())

// Check for any mobile device, excluding tablets.
if ($detect->isMobile() && !$detect->isTablet())

if ($detect->isMobile() && !$detect->isTablet())

// Alternative to $detect->isAndroidOS()
$detect->is('AndroidOS');
 
// Batch usage
foreach($userAgents as $userAgent){
  $detect->setUserAgent($userAgent);
  $isMobile = $detect->isMobile();
}
 
// Version check.
$detect->version('iPad'); // 4.3 (float)
```
下载地址: [http://mobiledetect.net/](http://mobiledetect.net/)