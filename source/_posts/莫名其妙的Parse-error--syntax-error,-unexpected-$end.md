---
title: "莫名其妙的Parse error: syntax error, unexpected $end"
categories: [ "PHP" ]
tags: [ "php","parse" ]
draft: false
slug: "reading-eighth-times-inexplicable-parse-error-syntax-error-unexpected-end"
date: "2017-11-16 13:07:00"
---

之前的项目都没有问题,重装系统后发现:`Parse error: syntax error, unexpected $end`.找了半天,竟然忘了GOOGLE一下..原来是这样哦,记录一下.

在PHP文件中如果使用了<?来替代<?php标记PHP代码的开始，并且php.ini中的short_open_tag应该为On
`short_open_tag = On`

否则就会报异常：`Parse error: syntax error, unexpected $end`
