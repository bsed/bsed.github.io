---
title: 如何不重新启动 Emacs 就让 .emacs 的配置起作用
date: 2012-09-13 20:54:00
updated: 2016-09-30 21:12:57
tags: 
- javascript
- jstips
categories: 
- js

---
我刚刚使用 Emacs 的时候，总是

vi ~/.emacs
然后重新启动 emacs ，效率很低 ，暗自嘟囔， emacs 怎么没有这种功能，不重起，就自动更新 .emacs 的设置 呢？

后来我发现，这个功能完全没有必要，我的做法是:

用 emacs 打开 .emacs 文件，C-x C-e 光标前面的运行一条语句。立即生效。
选择一个 region , M-x eval-region

    M-x load-file ~/.emacs
    M-x eval-buffer


<!--more-->


都是立即生效，可以马上试验一条语句的效果。 例如，在任何一个文件中，写

    (setq frame-title-format "emacs@%b")

把光标停在在这条语句后面， C-x C-e ，马上看到 emacs 的 标题栏上发生变化。

我用这种方法调试我的每一个小的配置文件，按上篇文章说的方法， 把他放在 ~/Emacs/myconfig/my-site-start.d 中。