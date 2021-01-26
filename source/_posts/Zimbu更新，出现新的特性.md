---
title: "Zimbu更新，出现新的特性"
categories: [ "Zimbu" ]
tags: [ "zimbu","update" ]
draft: false
slug: "Zimbu-snapshot-and-update-available"
date: "2014-12-30 14:30:00"
---

已经上传了一个新的快照，同样的代码也可以从
在`Mercurial`库。
主要的变化是，对象成员都标有$。此
基本上意味着“THIS。”它可以很容易地发现使用对象
变量和方法，也为一个非常大的类。它也允许在共享部分
使用相同的名称，参数和项目。


<!--more-->


例如：

    CLASS Pair
              string $key
              int    $value
    
              NEW(string key, int value)
                $key = key
                $value = value
              }
    
              FUNC string $getKey()
                return $key
              }
            }

用Mercurial 检测更新:

    hg clone https://zimbu.googlecode.com/hg/ zimbu

并更新：

     hg pull

该模块的文档可直接提供
在线：[https://zimbu.googlecode.com/hg/docs/index.html](https://zimbu.googlecode.com/hg/docs/index.html)

国内 访问不了google code hg 用不了  ，蛋疼的问题
我 放了个clone 的链接：[https://fatdebian-zimbu.googlecode.com/archive/daeea63b54f1b7ac65ac39974f9744e7f11858ff.zip](https://fatdebian-zimbu.googlecode.com/archive/daeea63b54f1b7ac65ac39974f9744e7f11858ff.zip)
百度云：
享受！

 /// Bram Moolenaar -- Bram@Moolenaar.net -- http://www.Moolenaar.net   \\\

///        sponsor Vim, vote for features -- http://www.Vim.org/sponsor/ \\\

\\\        download, build and distribute -- http://www.A-A-P.org        ///

 \\\            help me help AIDS victims -- http://ICCF-Holland.org    ///