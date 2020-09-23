---
title: Emacs中的一些关于多窗口基本命令
date: 2013-01-31 20:46:00
updated: 2016-01-28 20:46:57
tags: 
- algorithms
categories: 
- algorithms

---
# 打开一个新的window
* `C-x 2`
在当前窗口的下方打开一个窗口
* `C-x 3`
在当前窗口的右方打开一个窗口


<!--more-->


# 使用其他的窗口
* `C-x o`
选择另外一个窗口
* `C-M-v`
滚动下一个窗口的内容
* `M-x compare-windows`
在本窗口中发现下个与本窗口内容不同的另外一个窗口位置

# 在另外一个窗口显示
* C-x 4 b *bufname* RET
在另外一个窗口中打开名字为bufname的buffer
* C-x 4 f *filename* RET
在另外一个窗口中打开文件名为filename的的文件
* C-x 4 d *directory* RET
在另外一个窗口中打开文件夹名称为 directory的Dired buffer

# 关闭窗口
* C-x 0
关闭当前选择的窗口
* C-x 1
关闭出当前选择窗口的所有窗口
* C-x ^
让当前选择的窗口更高
* C-x }
让当前选择的窗口更宽

[参考原文地址][]

[参考原文地址]: https://www.cs.utah.edu/dept/old/texinfo/emacs18/emacs_21.html