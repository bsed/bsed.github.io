---
title: "snipmate.vim插件"
categories: [ "Vim" ]
tags: [ "vim","snipmate.vim" ]
draft: false
slug: "vim-script-snipmate"
date: "2014-04-23 10:15:00"
---

github：[https://github.com/msanders/snipmate.vim](https://github.com/msanders/snipmate.vim)

为了加快效率，当然需要vim能实现tab键补全代码的功能了。我使用的是snipmate.vim这个插件，当你安装了snipmate.vim，已经有一些snippets了，如ruby，php，java......


<!--more-->


比如在写ruby的时候，输入def在按tab键，就能显示为

    def method_name
    
    end

当然你可以修改相应的snippets，大概文件位置在这

`～/vimfiles/bundle/snipmate.vim/snippets`

特别注意的是其中的_.snippets文件，他可以设置全局snippets，就比如我在其中设置了

    snippet =
        <%= ${1} %>
    snippet <
        <% ${1} %>

用于快速输出

    <%  %>
    <%=  %>