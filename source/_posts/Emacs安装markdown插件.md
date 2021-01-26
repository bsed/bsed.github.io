---
title: "Emacs安装markdown插件"
categories: [ "Vim" ]
tags: [ "markdown","emacs" ]
draft: false
slug: "emacs-install-markdown-plugin"
date: "2014-12-07 20:41:00"
---

# 添加markdown扩展
## 安装markdown
  Ubuntu下输入命令
  
  ```bash
  sudo apt-get install markdown
  ```
  
## 安装markdown的插件

1. 需要安装`markdown-mode`进入emacs，输入`M-x el-get-install RET`
2. 找到`markdown-mode`，安装
3. 将下面的代码粘贴到.emcas文档


<!--more-->


```elisp
;;enable markdown mode
(autoload 'markdown-mode "markdown-mode.el"
"Major mode for editing Markdown files" t)
(add-to-list 'auto-mode-alist '("\\.text\\'" . markdown-mode))
(add-to-list 'auto-mode-alist '("\\.markdown\\'" . markdown-mode))
(add-to-list 'auto-mode-alist '("\\.md\\'" . markdown-mode))
```
    
## markdown 常用快捷键

###超链接
  * `C-c C-a l [文本](url)`
  * `C-c C-a L [文本](label)`
  * `C-c C-a u 直接插入链接`
  
###图像
  * `C-c C-i i`
  
###风格
  * `C-c C-s e 斜体`
  * `C-c C-s s 粗体`
  * `C-c C-s b 缩进`
  * `C-c C-s p 跳格`
  
###标题
  * `C-c C-t number 标题从1到6`
  
###查看效果
  * `C-c C-c m 运行markdown 并且查看HTML代码`
  * `C-c C-c p 运行markdown 并打开浏览器预览`
  