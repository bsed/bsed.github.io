---
title: vim、gvim在windows下中文乱码的终极解决方案
date: 2010-05-16 13:43:00
updated: 2015-05-16 13:44:15
tags: 
- design
categories: 
- default

---
在windows下vim的中文字体显示并不好，所以我们需要调整字体显示编码为utf-8，只有这样才能使用美观的中文。这时候可能出现很多中文乱码。
如：
文件显示乱码、菜单乱码、右键菜单乱码、conlse输出乱码、文件关联右键菜单乱码等等。
网上很多解决办法，但我试了很多，都出现一些怪异的乱码，顾此失彼。


<!--more-->


有个简单的方法解决乱码问题：删除vim下的lang目录。但这样所有菜单只能显示英文了。
于是只好自己想办法。经过长时间摸索，总结下边的代码可以彻底解决这些问题。

    """""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
    " vim7.4在windows下的编码设置
    """"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
    set encoding=utf-8
    set fileencodings=utf-8,chinese,latin-1
    if has("win32")
    set fileencoding=chinese
    else
    set fileencoding=utf-8
    endif
    "解决菜单乱码
    source $VIMRUNTIME/delmenu.vim
    source $VIMRUNTIME/menu.vim
    "解决consle输出乱码
    language messages zh_CN.utf-8