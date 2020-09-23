---
title: 中国区加速GitHub访问速度
date: 2020-04-04 13:54:00
updated: 2020-04-04 14:19:33
tags: 
- word
- wps2019
- key
categories: 
- os

---
由于某些原因，国内访问Github有时会异常缓慢，通过修改系统hosts文件的办法，绕过国内dns解析，直接访问GitHub的CDN节点，从而达到加速的目的。

**实现**

在本地host文件中添加映射，步骤如下：

用文本编辑器打开hosts文件（注意修改访问权限），位于`C:\Windows\System32\drivers\etc` 目录下

打开 [http://tool.chinaz.com/dns](http://tool.chinaz.com/dns) ，这是一个查询域名映射关系的工具


<!--more-->


重点查询`github.global.ssl.fastly.net` 和`assets-cdn.github.com`两个地址

多查几次，选择一个稳定，延迟较低的ip按如下方式添加到host文件

比如将下列信息添加到host文件中：

# github
52.74.223.119 github.com

185.199.109.153 developer.github.com

185.199.111.153 developer.github.com
185.199.108.153 developer.github.com
185.199.110.153 developer.github.com
185.199.109.153	developer.github.com
203.208.39.99 developer.github.com

185.199.111.153	assets-cdn.github.com
185.199.108.153 assets-cdn.github.com
185.199.110.153 assets-cdn.github.com
185.199.109.153 assets-cdn.github.com
203.208.39.99 assets-cdn.github.com

199.59.148.209 github.global.ssl.fastly.net
59.24.3.173 github.global.ssl.fastly.net
203.208.39.99 github.global.ssl.fastly.net

保存文件，`Windows+X` 打开系统命令行（管理员身份）或`powershell`

运行`ipconfig /flushdns`手动刷新系统DNS缓存。

重新打开浏览器，完成加速。