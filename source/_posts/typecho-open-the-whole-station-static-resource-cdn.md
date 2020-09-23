---
title: typecho开启全站静态资源cdn
date: 2020-03-29 14:42:00
updated: 2020-03-29 14:44:17
tags: 
- ubuntu
- rime
categories: 
- linux

---
笔者最近更换了域名为 [tygasoft.com](tygasoft.com)

**总结如下**：

## 开启CDN
啥是CDN，我这里就不详细介绍了,具体可以谷歌。

## 步骤

### 添加typecho函数

<!--more-->


打开`typecho`主目录下的`config.inc.php`，（看清楚不是主题目录，是网站的主目录），在首行的`<?php` 后添加以下代码：
```php
/* 定义插件cdn目录(相对路径) */
define('__TYPECHO_CDN_DIR__', 'cdn.gnux.cn/');

```
### 修改相关代码

接着打开`var/WidgetOptions.php`，大概第160行，找到以下代码：
```php
protected function ___themeUrl()
{
   return defined('__TYPECHO_THEME_URL__') ? __TYPECHO_THEME_URL__ :
   Typecho_Common::url(__TYPECHO_THEME_DIR__ . '/' . $this->theme, $this->siteUrl);
}

/**
 * 获取插件路径
 *
 * @access protected
 * @return string
 */
protected function ___pluginUrl()
{
 return defined('__TYPECHO_PLUGIN_URL__') ? __TYPECHO_PLUGIN_URL__ :
 Typecho_Common::url(__TYPECHO_PLUGIN_DIR__, $this->siteUrl);
}

```
替换成：
```php
protected function ___themeUrl()
 {
   return defined('__TYPECHO_THEME_URL__') ? __TYPECHO_THEME_URL__ :
   Typecho_Common::url(__TYPECHO_THEME_DIR__ . '/' . $this->theme,__TYPECHO_CDN_DIR__);
  }

/**
* 获取插件路径
*
* @access protected
* @return string
*/
protected function ___pluginUrl()
  {
   return defined('__TYPECHO_PLUGIN_URL__') ? __TYPECHO_PLUGIN_URL__ :
   Typecho_Common::url(__TYPECHO_PLUGIN_DIR__,__TYPECHO_CDN_DIR__);
  }
```
## 测试效果
此时，到首页和文章页`ctrl+F5` 刷新几遍，看看你的css、js静态资源是不是变成你的cdn加速域名啦！！