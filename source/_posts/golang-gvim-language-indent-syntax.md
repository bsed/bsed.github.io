---
title: 基于GVIM的GOLANG语法高亮和自动补全
date: 2014-11-23 22:19:00
updated: 2014-12-25 11:58:37
tags: 
- ajax
- pjax
- pushstate
categories: 
- js

---
Golang是Google推出的一种全新的计算机编程语言。可以在不损失应用程序性能的情况下降低代码复杂率，特点是简洁、易用。
这里主要介绍Windows下的Golang的安装和配置

1.安装Golang.


<!--more-->


现在Windows下Golang的安装方法非常简单，可以说是一键安装。连环境变量都不用填。一路Next，直接就好了。如果没有自动填写好的话，可以手动增加。当然选择安装路径的时候最好选择默认的。也就是C:Go。Golang的下载链接[https://code.google.com/p/go/downloads/list](https://code.google.com/p/go/downloads/list)。1.2 之前版本
1.2之后见官网 [http://gorlang.org](http://gorlang.org)
1.5 以后 见github.com（O(∩_∩)O~ 大公司也开始用github了，记一下）

2.gVim配置

先安装gVim,然后把Go文件夹下的文件相应的复制到gVim的文件夹下就可了。然后是配置_vimrc文件。
下载gVim
在配置文件_vimrc中加入下面几条：

    set rtp+=$GOROOT/misc/vim
    filetype plugin indent on

syntax on

3.安装neocomplcache.

neocomplcache是一款gVim的自动补全插件。它使用缓存，速度快，生成的关键词较准确。
下载[neocomplcache ](http://www.vim.org/scripts/download_script.php?src_id=20093)
下面是配置NeoCompCache。这是用TAB键上屏的方式，如果你想用Enter键上屏也可以，把   SuperTab like snippets behavior.下面那句注释掉。然后然后用

    "AutoComplPop like behavior.
    let g:neocomplcache_enable_auto_select = 1

AutoComplPop就是用Enter上屏的。
效果如下：
![20120702_202746.png][1]

 呵呵 在贴个配置：

    "Golang
    " Use neocomplcache.
    let g:neocomplcache_enable_at_startup = 1
    "Use smartcase.
    let g:neocomplcache_enable_smart_case = 1
    " Use camel case completion.
    let g:neocomplcache_enable_camel_case_completion = 1
    " Use underbar completion.
    let g:neocomplcache_enable_underbar_completion = 1
    " Set minimum syntax keyword length.
    let g:neocomplcache_min_syntax_length = 3
    let g:neocomplcache_lock_buffer_name_pattern = '*ku*'
    " Define dictionary. 
    let g:neocomplcache_dictionary_filetype_lists = { 
        \ 'default' : '', 
        \ 'vimshell' : $HOME.'/.vimshell_hist', 
        \ 'scheme' : $HOME.'/.gosh_completions' 
        \ } 
    " Define keyword.
    if !exists('g:neocomplcache_keyword_patterns')
    let g:neocomplcache_keyword_patterns = {}
    endif
    let g:neocomplcache_keyword_patterns['default'] = 'hw*'
     "Plugin key-mappings.
    imap <C-k> <Plug>(neocomplcache_snippets_expand)
    smap <C-k> <Plug>(neocomplcache_snippets_expand)
    inoremap <expr><C-g> neocomplcache#undo_completion()
    inoremap <expr><C-l> neocomplcache#complete_common_string()
    "SuperTab like snippets behavior.
    "imap <expr><TAB> neocomplcache#sources#snippets_complete#expandable() ? "<Plug>(neocomplcache_snippets_expand)" : pumvisible() ? "<C-n>" : "<TAB>"
    " Recommended key-mappings.
    " <CR>: close popup and save indent.
    inoremap <expr><CR> neocomplcache#smart_close_popup() . "<CR>"
    "<TAB>: completion.
    inoremap <expr><TAB> pumvisible() ? "<C-n>" : "<TAB>"
    "<C-h>, <BS>: close popup and delete backword char.
    inoremap <expr><C-h> neocomplcache#smart_close_popup()."<C-h>"
    inoremap <expr><BS> neocomplcache#smart_close_popup()."<C-h>"
    inoremap <expr><C-y> neocomplcache#close_popup()
    inoremap <expr><C-e> neocomplcache#cancel_popup()
    "AutoComplPop like behavior.
    "let g:neocomplcache_enable_auto_select = 1
    " Shell like behavior(not recommended).
    "set completeopt+=longest
    "let g:neocomplcache_enable_auto_select = 1
    "let g:neocomplcache_disable_auto_complete = 1
    "inoremap <expr><TAB> pumvisible() ? "<Down>" : "<TAB>"
    "inoremap <expr><CR> neocomplcache#smart_close_popup() . "<CR>"
    "Enable omni completion.
    autocmd FileType css setlocal omnifunc=csscomplete#CompleteCSS
    autocmd FileType html,markdown setlocal omnifunc=htmlcomplete#CompleteTags
    autocmd FileType javascript setlocal omnifunc=javascriptcomplete#CompleteJS
    autocmd FileType python setlocal omnifunc=pythoncomplete#Complete
    autocmd FileType xml setlocal omnifunc=xmlcomplete#CompleteTags
    "Enable heavy omni completion.
    if !exists('g:neocomplcache_omni_patterns')
    let g:neocomplcache_omni_patterns = {}
    endif
    let g:neocomplcache_omni_patterns.ruby = '[^. *t].w*|hw*::'
    "autocmd FileType ruby setlocal omnifunc=rubycomplete#Complete
    let g:neocomplcache_omni_patterns.php = '[^. t]->hw*|hw*::'
    let g:neocomplcache_omni_patterns.c = '%(.|->)hw*'
    let g:neocomplcache_omni_patterns.cpp = 'hw*%(.|->)hw*|hw*::'

推荐一篇文章：[http://www.cnblogs.com/xiekeli/archive/2012/08/13/2637176.html](http://www.cnblogs.com/xiekeli/archive/2012/08/13/2637176.html)
文章补充：附:创建其他的tags文件: 
在命令行模式下进入库文件的目录（一般为include和src），执行
`ctags   -R   --c++-kinds=+p   --fields=+iaS   --extra=+q`，然后在该库文件目录下即可得到一个名为tags的文件，更名后复制到自己的tags目录下，并在_vimrc中设置。


  [1]: https://imgs.gnux.cn/usr/uploads/2014/11/908736786.png