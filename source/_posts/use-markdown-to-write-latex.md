---
title: 用markdown来写LaTeX
date: 2013-10-22 13:50:00
updated: 2015-01-12 13:54:56
tags: 
- java
categories: 
- java

---
注：[LaTeX+gVim+SumatraPDF](http://laoyang945.github.com/latex-gvim-sumatrapdf-setting/)设置这篇文章里最后_vimrc设置有修改，将pdflatex改为xelatex，以便方便的用在LaTeX里面使用中文。

自从用了`markdown`这个轻量级标记语言，我是觉得写东西的感觉顺畅了许多，再加上Vim这个超级好用的编辑器，想不写东西都难。慢慢的拾起自己的读书笔记吧。


<!--more-->


现在的问题是，我的文献综述是在LaTeX下面写的，在排版的时候倒是很方便，基本不用我自己操心什么的，但是写作的时候感觉着实是不好，特别是在分章节、加粗体的时候，操作相当的“重”，我就想着能不能把markdown和LaTeX结合起来，公式什么的使用LaTeX，在输入的时候使用markdown的风格。

在Google上搜索了一番，还真的让我找到了[pandoc](http://johnmacfarlane.net/pandoc/)这个软件，让人惊喜的是他居然还支持参考文献的引用和列表生成，那我所有的需求都可以满足了。如何使用请查阅pandoc的用户手册

现在我用markdown文件写文献综述或者文献笔记，用markdown+LaTeX的语法，通过pandoc把markdown文件转换为LaTeX文本（如果没有参考文献的需要可以直接把markdown转化为pdf）。

为了在markdown里面更好的区分LaTeX代码，需要修改一下Vim的markdown语法高亮文件，路径在vim73/syntax/markdown.vim，添加以下几行：

    syn region markdownTexInline start="\$" end="\$" keepend oneline
    syn region markdownTexBlock start="\\begin{.*}" end="\\end{.*}" keepend 
    hi def link markdownTexBlock Tag
    hi def link markdownTexInline Tag

这样在markdown文件里面就可以高亮显示用LaTeX写的多数数学式子了，实在是太方便。