---
title: "easymotion.vim 使用方法"
categories: [ "Vim" ]
tags: [ "vim","easymotion.vim" ]
draft: false
slug: "easymotionvim-usage-method"
date: "2020-09-22 20:24:00"
---

github: [https://github.com/Lokaltog/vim-easymotion](https://github.com/Lokaltog/vim-easymotion)

**注释**: `<leader>`全局映射为,

除却`hjkl`, `gg`, `G`, `Ctrl-D/U`, 以及 `[f/F]<char>`和`[t/T]<char>`这些vim默认的移动方式

有没有更高效的移动做法么? 当然有了，easymotion.vim , 这个插件的唯一目的就是: 快速跳转

**作用**: 如何进行更快速的光标移动

## 安装

```
Bundle 'Lokaltog/vim-easymotion'
```
## 用法1: 跳转到当前光标前后的位置(`w/b`)

快捷键`<leader><leader>w`(即`,,w`)和`<leader><leader>b`(即`,,b`)

**助记**: `word and back`

演示:

![easy_motion_base.gif][1]


<!--more-->


## 用法2: 搜索跳转(s)

快捷键`<leader><leader>`s(即`,,s`), 然后输入要搜索的字母, 这个跳转是双向的

**助记**: search

演示:

![easy_motion_search.gif][2]

## 用法3: 行级跳转(`jk`)

配置

```
map <Leader><Leader>j <Plug>(easymotion-j)
map <Leader><Leader>k <Plug>(easymotion-k)
```
快捷键: `<leader><leader>j`和`<leader><leader>k`(即`,,j`和`,,k`)

助记: `hjkl`

  [1]: https://imgs.gnux.cn/usr/uploads/2020/09/2750825277.gif
  [2]: https://imgs.gnux.cn/usr/uploads/2020/09/2496289656.gif