---
title: 我的一些emacs的基础配置
date: 2014-01-28 20:39:00
updated: 2016-01-28 20:40:01
tags: 
- rails
- mixin
categories: 
- rails

---
## 将下面的package archive的link添加到.emacs中去
```elisp
(require 'package) 
(setq package-archives '(("gnu" . "http://elpa.gnu.org/packages/")
             ("marmalade" . "http://marmalade-repo.org/packages/") 
             ("melpa" . "http://melpa.milkbox.net/packages/")))
```
  如果你想浏览所有的包文件可以使用命令 **M-x package-list-packages**; 如果想要安装新的el包，那么可以输入命令**M-x package-install RET**来进行安装啦

## 选择一个合适的emacs皮肤
 * 输入 **`M-x customize-themes`** 选择合适的皮肤

## 设置默认启动emacs的时候为全屏
  * 将下面的代码添加到**.emacs**文件中
  
    ```elisp
    ;;start fullscreen
    (defun toggle-fullscreen ()
    (interactive)
    (x-send-client-message nil 0 nil "_NET_WM_STATE" 32
    '(2 "_NET_WM_STATE_MAXIMIZED_VERT" 0))
    (x-send-client-message nil 0 nil "_NET_WM_STATE" 32
    '(2 "_NET_WM_STATE_MAXIMIZED_HORZ" 0))
    )
    (toggle-fullscreen)
    ```


<!--more-->


## 设置emacs退出的时候，保存当前的buffer
* 将下面的代码加入到**.emacs**文件中

    ```elisp
    ;;autosave buffers
    (require 'desktop)
    (desktop-save-mode 1)
    (defun my-desktop-save ()
    (interactive)
    ;; Don't call desktop-save-in-desktop-dir, as it prints a message.
    (if (eq (desktop-owner) (emacs-pid))
    (desktop-save desktop-dirname)))
    (add-hook 'auto-save-hook 'my-desktop-save)
    ```
  
## 设置缩进
* 将下面的代码粘贴到**.emacs**文件中去

    ```elisp
    (setq-default indent-tabs-mode nil)
    (setq-default tab-width 4)
    (setq tab-stop-list (number-sequence 4 200 4))
    ```

## 安装el-get
el-get功能与package.el类似，但是他会自动的安装依赖的包，安装方法如下：

1. 输入`C-x b scratch RET`进入 \*scratch* buffer
2. 将下面的代码粘贴到buffer,然后`C-j`运行，之后你就可以得到deverloper版本的el-get了

    ```elisp
    ;; So the idea is that you copy/paste this code into your \*scratch* buffer,
    ;; hit C-j and you have a working developper editon of el-get.
    (url-retrieve "https://raw.github.com/dimitri/el-get/master/el-get-install.el" (lambda (s) (let (el-get-master-branch) (goto-char (point-max)) (eval-print-last-sexp))))
    ```
    
3. 在表达式末尾按`C-j`，运行，然后将下面的代码贴到 .emacs文件

    ```elisp
    ;;el-get
    ;;https://github.com/dimitri/el-get
    (add-to-list 'load-path "~/.emacs.d/el-get/el-get")
    (unless (require 'el-get nil 'noerror)
    (with-current-buffer
    (url-retrieve-synchronously
    "https://raw.github.com/dimitri/el-get/master/el-get-install.el")
    (let (el-get-master-branch)
    (goto-char (point-max))
    (eval-print-last-sexp))))
    (el-get 'sync)
    ```
    
4. 现在你可以输入`M-x el-get-list-package`来列出包，然后使用`M-x el-get-install`然后输入包的名字进行安装

## 安装Jedi.el

Jedi.el是Python在emacs下的一个自动补全的包，首先需要安装virtualenv

```bash
sudo apt-get install python-virtualenv
```

然后就可以安装Jedi了，使用命令`M-x el-get-install jedi`, 使Jedi激活，需要将下面的代码添加到**.emacs**文件中

```elisp
;;enable jedi autocompletion
(add-hook 'python-mode-hook 'auto-complete-mode)
(add-hook 'python-mode-hook 'jedi:ac-setup)
```

## 安装flycheck

flycheck 是一个实时语法检测插件，使用`M-x package-install flycheck`安装，然后将下面的内容添加到**.emacs**文件中：

```elisp
;;enable flycheck
(add-hook 'after-init-hook #'global-flycheck-mode)
```

## 安装Autopair

Autopair是一个自动匹配括号和引号的插件，使用命令`M-x package-install autopair`安装然后将下面的代码贴到**.emacs**文件中

```elisp
(require 'autopair)
(autopair-global-mode) ;; to enable in all buffers
```

## 显示行号

```elisp
;;enable linum mode
(add-hook 'python-mode-hook 'linum-mode)
```
## 添加markdown扩展

首先需要安装markdown-mode`M-x el-get-install RET`，然后找到markdown-mode，安装，接着，将下面的代码粘贴到.emcas文档

```elisp
;;enable markdown mode
(autoload 'markdown-mode "markdown-mode.el"
   "Major mode for editing Markdown files" t)
(add-to-list 'auto-mode-alist '("\\.text\\'" . markdown-mode))
(add-to-list 'auto-mode-alist '("\\.markdown\\'" . markdown-mode))
(add-to-list 'auto-mode-alist '("\\.md\\'" . markdown-mode))
```

## 输入中文

* 使用搜狗输入法的时候，如果是在中文版本的ubuntu那么不用配置，直接就可以使用，可以将启动的快捷键`Ctrl + Space`改为其他的
* 如果是使用的英文版，那么需要启动的时候，添加中文的locale

  ```bash
  env LANG=zh_CN.UTF-8 emacs
  ```