---
title: [已解决]git中push origin master出错：error: src refspec master does not match any
date: 2015-02-13 16:50:00
updated: 2015-02-28 14:19:53
tags: 
- ANDROID-DEVELOPER
categories: 
- android

---
解决git中push origin master出错：error: src refspec master does not match any

先去add，再commit，然后再push，就可以了：


<!--more-->


 解决github push错误The requested URL returned error: 403 Forbidden while accessing
git version 1.7.1
OS:CENTOS
解决方案：

vim .git/config

修改

[remote "origin"]  
    url = https://github.com/wangz/example.git  
为：
[remote "origin"]  
    url = https://wangz@github.com/wangz/example.git  
再次git push，弹出框输入密码，即可提交

http://blog.csdn.net/chain2012/article/details/7476493

http://michaelye1988.iteye.com/blog/1637951