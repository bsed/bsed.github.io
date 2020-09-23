---
title: Use .httaccess to redirect 301 http  to https and index.php
date: 2013-09-24 13:46:00
updated: 2016-09-24 13:53:32
tags: 
- javascript
- jstips
categories: 
- js

---
```.htaccess
RewriteEngine On
RewriteBase /

#if the request is not secure http?
RewriteCond %{HTTPS} off
#redirect to the secure version
RewriteRule (.*) https://%{HTTP_HOST}/$1 [R=301,L]

#These are your existing rules
RewriteCond %{REQUEST_FILENAME} !.(jpg|jpeg|gif|png|css|js|pl|txt)$ [NC]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*) index.php
#RewriteRule . /index.php [L]
```

参考：

- [http://stackoverflow.com/questions/32049820/use-htaccess-to-redirect-http-to-https](http://stackoverflow.com/questions/32049820/use-htaccess-to-redirect-http-to-https)
- [http://stackoverflow.com/questions/8625213/htaccess-and-ssl-and-redirect-to-index-php](http://stackoverflow.com/questions/8625213/htaccess-and-ssl-and-redirect-to-index-php)