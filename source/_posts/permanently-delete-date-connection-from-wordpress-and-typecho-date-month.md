---
title: 从WordPress、Typecho 永久删除日期连接（年月日）
date: 2018-07-13 08:44:00
updated: 2018-07-18 16:55:11
tags: 
- javascript
- vue
categories: 
- js

---
随着“短网址”的兴起，包括网址中的日期不再被视为“酷”，因此您可能希望“随身携带”并愚弄您的WordPress/Typecho网址以缩短其时间。以下是如何使用.htaccess从WordPress/Typecho永久链接中删除日期。
想移除年/月/日/日期连接，需要将下列内容添加到`.htaccess`
:smile:
## 第1步：更新WP永久链接设置
登录WP Admin并将固定链接结构更改为您想要的链接结构; 
```bash
/%postname%/
```
Typecho 结构为：
```bash
 默认风格 /archives/{cid}/
 wordpress风格 /archives/{slug}.html
 按日期归档 /archives/{year}/{month}/{day}/{slug}.html
 按分类归档 /{category}/{slug}.html
 个性化定义 
/{slug}.html
```
## 第2步：使用`mod_rewrite`重定向旧URL
接下来，将以下任一项添加到您站点的根**.htaccess**文件中：
⚡
<!--more-->

如果你的永久链接看起来像这样： `/%year%/%monthnum%/%day%/%postname%/`

然后添加这个.htaccess代码： `RewriteRule ^([0-9]+)/([0-9]+)/([0-9]+)/(.*)$ /$4 [R=301,NC,L]`

如果你的永久链接看起来像这样： `/%year%/%monthnum%/%postname%/`

然后添加这个.htaccess代码： `RewriteRule ^([0-9]+)/([0-9]+)/(.*)$ /$3 [R=301,NC,L]`

如果你的永久链接看起来像这样： `/%year%/%postname%/`

然后添加这个.htaccess代码： `RewriteRule ^([0-9]+)/(.*)$ /$3 [R=301,NC,L]`

如果您想合并代码，可以将此步骤中的内容与**WordPress永久链接**规则或者**Typecho永久链接**规则（也位于.htaccess文件中）结合使用。这是一个例子：

Apache:
```bash
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^([0-9]+)/([0-9]+)/(.*)$ /$3 [R=301,NC,L]
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
</IfModule>
```
这就是它的全部内容。应该提到的是，这种方法使用Apache mod_rewrite进行重定向，但这并不是唯一的方法。

## 备用步骤2：使用mod_alias重定向旧URL
我们可以使用mod_alias而不是使用Apache的重写模块来重定向旧链接。将这些可能的指令中的任何一个应用于根`.htaccess`文件：
如果你的OLD永久链接看起来像这样： `/%year%/%monthnum%/%day%/%postname%/`

然后用这个： `RedirectMatch 301 ^/[0-9]{4}/[0-9]{2}/[0-9]{2}/([a-z0-9\-/]+) http://example.com/$1`

如果你的永久链接看起来像这样： `/%year%/%monthnum%/%postname%/`

然后用这个： `RedirectMatch 301 ^/[0-9]{4}/[0-9]{2}/([a-z0-9\-/]+) http://example.com/$1`

如果你的永久链接看起来像这样： `/%year%/%postname%/`

然后用这个： `RedirectMatch 301 ^/[0-9]{4}/([a-z0-9\-/]+) http://example.com/$1`

## 结果
一旦完成第1步和第2步，恭喜，你可以用新链接访问。

https://tygasoft.com/2018/07/08/reflection-on-film-and-television-i-am-not-a-medicine-god.html
..到这个：

https://tygasoft.com/reflection-on-film-and-television-i-am-not-a-medicine-god.html