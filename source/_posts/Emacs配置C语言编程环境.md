---
title: "Emacs配置C语言编程环境"
categories: [ "Linux" ]
tags: [ "cpp","emacs" ]
draft: false
slug: "emacs-configuration-c-language-programming-environment"
date: "2014-10-22 10:42:00"
---

有很多优秀的C语言IDE用来实现高效的编程，比如[CLion](https://www.jetbrains.com/clion/)。目前，CLion还在处于公开测试阶段，可以下载试用。本文介绍在Emacs下配置C/C++编程环境。

## 1. 安装和基本配置

### 1.1. 安装需求

- 安装[GNU Global](http://www.gnu.org/software/global/)

`# dnf install global llvm llvm-devel clang* `


<!--more-->


- 安装Emacs的`helm`、`helm-swoop`、`helm-gtags`、`sr-speedbar`、`smartparens`、`google-c-style`、`auto-complete`、`auto-complete-c-headers`、`auto-complete-clang`包。

### 1.2. Emacs配置

将[附加环境设置](http://yulongniu.bionutshell.org/blog/2014/12/02/emacs-config-c/#c-mode-config)添加到Emacs设置文件，比如`~/.emacs`，此环境配置参考[tuhdo](https://github.com/tuhdo/emacs-c-ide-demo/blob/master/custom/setup-helm.el)。

## 2. 文件操作

### 2.1. 查找

- 当前buffer查找函数、类的定义

`C-c h i`（helm-semantic-or-imenu）：可以输入部分关键词、空格等，以快速搜索。

- 当前project查找定义

1. `M-.`（helm-gtags-dwim）：输入查找内容。`M-,`（tags-loop-continue）：返回上一个buffer。
2. `C-j`（helm-gtags-select）：列出当前project所有定义，并且查找。

- 查找引用

1. `C-c g r`（helm-gtags-find-rtags）：光标停留在函数内部，查找该函数名；如果光标停留在函数名，查找该函数的所有引用。
2. `C-c g s`（helm-gtags-find-symbol）：光标停留在变量时的查找。

- 查找当前函数引用的函数

`C-c g a`（helm-gtags-tags-in-this-function）

- 查找文件

`C-c g P`（elm-gtags-find-files）

### 2.2. 列出源码树

- 开启和关闭

`M-x sr-speedbar-open RET`：开启。`M-x sr-speedbar-close RET`：关闭。

- 操作

1. `b`和`f`切换到当前buffer和所有文件列表。
2. `=`：展开。`g`：收回。
3. `U`：上一级目录。

### 2.3 代码补全

代码补全使用`auto-complete`、`auto-complete-c-headers`、`semantic`包。在使用`auto-complete-c-headers`自动补全头文件名称时，使用以下命令获得头文件路径：

`$ gcc -xc -E -v -$ gcc -xc++ -E -v -`

![images](http://yulongniu.bionutshell.org/images/emacs_config_c_autocomplete.png)

## 3. 编译和debug

### 3.1 使用gcc编译

使用`M-x compile`对源文件进行编译链接；或者开启ansi-term模式`M-x ansi-term`，进入shell操作。一个直接编译–汇编–链接的简单例子：

```
$ gcc helloworld.c -o helloworld
$ ./helloworld

```
## 附加内容

[C/C++的Emacs编程环境设置](undefined)

Emacs configure
```
;;;;;;;;;;;;;;;;;;;;;;;;;
;set up helm
;;;;;;;;;;;;;;;;;;;;;;;;;
(require 'helm-config)
(require 'helm-grep)
;; The default "C-x c" is quite close to "C-x C-c", which quits Emacs.
;; Changed to "C-c h". Note: We must set "C-c h" globally, because we
;; cannot change `helm-command-prefix-key' once `helm-config' is loaded.
(global-set-key (kbd "C-c h") 'helm-command-prefix)
(global-unset-key (kbd "C-x c"))

(define-key helm-map (kbd "<tab>") 'helm-execute-persistent-action) ; rebihnd tab to do persistent action
(define-key helm-map (kbd "C-i") 'helm-execute-persistent-action) ; make TAB works in terminal
(define-key helm-map (kbd "C-z")  'helm-select-action) ; list actions using C-z

(define-key helm-grep-mode-map (kbd "<return>")  'helm-grep-mode-jump-other-window)
(define-key helm-grep-mode-map (kbd "n")  'helm-grep-mode-jump-other-window-forward)
(define-key helm-grep-mode-map (kbd "p")  'helm-grep-mode-jump-other-window-backward)

(when (executable-find "curl")
  (setq helm-google-suggest-use-curl-p t))

(setq
 helm-scroll-amount 4 ; scroll 4 lines other window using M-<next>/M-<prior>
 helm-quick-update t ; do not display invisible candidates
 helm-ff-search-library-in-sexp t ; search for library in `require' and `declare-function' sexp.
 helm-split-window-in-side-p t ;; open helm buffer inside current window, not occupy whole other window
 helm-candidate-number-limit 500 ; limit the number of displayed canidates
 helm-ff-file-name-history-use-recentf t
 helm-move-to-line-cycle-in-source t ; move to end or beginning of source when reaching top or bottom of source.
 helm-buffers-fuzzy-matching t          ; fuzzy matching buffer names when non-nil
                                        ; useful in helm-mini that lists buffers

 )

(add-to-list 'helm-sources-using-default-as-input 'helm-source-man-pages)

(global-set-key (kbd "M-x") 'helm-M-x)
(global-set-key (kbd "M-y") 'helm-show-kill-ring)
(global-set-key (kbd "C-x b") 'helm-mini)
(global-set-key (kbd "C-x C-f") 'helm-find-files)
(global-set-key (kbd "C-h SPC") 'helm-all-mark-rings)
(global-set-key (kbd "C-c h o") 'helm-occur)

(global-set-key (kbd "C-c h C-c w") 'helm-wikipedia-suggest)

(global-set-key (kbd "C-c h x") 'helm-register)
;; (global-set-key (kbd "C-x r j") 'jump-to-register)

(define-key 'help-command (kbd "C-f") 'helm-apropos)
(define-key 'help-command (kbd "r") 'helm-info-emacs)
(define-key 'help-command (kbd "C-l") 'helm-locate-library)

;; use helm to list eshell history
(add-hook 'eshell-mode-hook
          #'(lambda ()
              (define-key eshell-mode-map (kbd "M-l")  'helm-eshell-history)))

;;; Save current position to mark ring
(add-hook 'helm-goto-line-before-hook 'helm-save-current-pos-to-mark-ring)

;; show minibuffer history with Helm
(define-key minibuffer-local-map (kbd "M-p") 'helm-minibuffer-history)
(define-key minibuffer-local-map (kbd "M-n") 'helm-minibuffer-history)

(define-key global-map [remap find-tag] 'helm-etags-select)

(define-key global-map [remap list-buffers] 'helm-buffers-list)

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; PACKAGE: helm-swoop                ;;
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; Locate the helm-swoop folder to your path
(require 'helm-swoop)

;; Change the keybinds to whatever you like :)
(global-set-key (kbd "C-c h o") 'helm-swoop)
(global-set-key (kbd "C-c s") 'helm-multi-swoop-all)

;; When doing isearch, hand the word over to helm-swoop
(define-key isearch-mode-map (kbd "M-i") 'helm-swoop-from-isearch)

;; From helm-swoop to helm-multi-swoop-all
(define-key helm-swoop-map (kbd "M-i") 'helm-multi-swoop-all-from-helm-swoop)

;; Save buffer when helm-multi-swoop-edit complete
(setq helm-multi-swoop-edit-save t)

;; If this value is t, split window inside the current window
(setq helm-swoop-split-with-multiple-windows t)

;; Split direcion. 'split-window-vertically or 'split-window-horizontally
(setq helm-swoop-split-direction 'split-window-vertically)

;; If nil, you can slightly boost invoke speed in exchange for text color
(setq helm-swoop-speed-or-color t)

(helm-mode 1)

(provide 'setup-helm)

(require 'setup-helm)
(setq
 helm-gtags-ignore-case t
 helm-gtags-auto-update t
 helm-gtags-use-input-at-cursor t
 helm-gtags-pulse-at-cursor t
 helm-gtags-prefix-key "\C-cg"
 helm-gtags-suggested-key-mapping t
 )

(require 'helm-gtags)
;; Enable helm-gtags-mode
(add-hook 'dired-mode-hook 'helm-gtags-mode)
(add-hook 'eshell-mode-hook 'helm-gtags-mode)
(add-hook 'c-mode-hook 'helm-gtags-mode)
(add-hook 'c++-mode-hook 'helm-gtags-mode)
(add-hook 'asm-mode-hook 'helm-gtags-mode)

(define-key helm-gtags-mode-map (kbd "C-c g a") 'helm-gtags-tags-in-this-function)
(define-key helm-gtags-mode-map (kbd "C-j") 'helm-gtags-select)
(define-key helm-gtags-mode-map (kbd "M-.") 'helm-gtags-dwim)
(define-key helm-gtags-mode-map (kbd "M-,") 'helm-gtags-pop-stack)
(define-key helm-gtags-mode-map (kbd "C-c <") 'helm-gtags-previous-history)
(define-key helm-gtags-mode-map (kbd "C-c >") 'helm-gtags-next-history)

;;;;;;;;;;;;;;;;;;;;;
;enable semantic mode
;;;;;;;;;;;;;;;;;;;;;
(require 'cc-mode)
(require 'semantic)

(global-semanticdb-minor-mode 1)
(global-semantic-idle-scheduler-mode 1)
(global-semantic-idle-summary-mode 1)
(global-semantic-stickyfunc-mode 1)

(semantic-mode 1)

;;;;;;;;;;;;;;;;;;;;;;;;
;open hs-mode
;;;;;;;;;;;;;;;;;;;;;;;
(add-hook 'c-mode-common-hook   'hs-minor-mode)

;;;;;;;;;;;;;;;;;;
;google-c-style
;;;;;;;;;;;;;;;;;;
(add-hook 'c-mode-common-hook 'google-set-c-style)
(add-hook 'c-mode-common-hook 'google-make-newline-indent)

;;;;;;;;;;;;;;;;;;;;;
;smartparens
;;;;;;;;;;;;;;;;;;;;;
(require 'smartparens-config)
(show-smartparens-global-mode t)
(smartparens-global-mode t)

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
; auto-complete
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
(require 'auto-complete)
(require 'auto-complete-config)
(ac-config-default)

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; auto-complete-c-headers 
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
(defun my:ac-c-header-init()
  (require 'auto-complete-c-headers)
  (add-to-list 'ac-sources 'ac-source-c-headers)
  (add-to-list 'achead:include-directories '"/usr/lib/gcc/x86_64-redhat-linux/4.9.2/../../../../include/c++/4.9.2")
  (add-to-list 'achead:include-directories '"/usr/lib/gcc/x86_64-redhat-linux/4.9.2/../../../../include/c++/4.9.2/x86_64-redhat-linux")
  (add-to-list 'achead:include-directories '"/usr/lib/gcc/x86_64-redhat-linux/4.9.2/../../../../include/c++/4.9.2/backward")
  (add-to-list 'achead:include-directories '"/usr/lib/gcc/x86_64-redhat-linux/4.9.2/include")
  (add-to-list 'achead:include-directories '"/usr/local/include")
  (add-to-list 'achead:include-directories '"/usr/include")
  )
(add-hook 'c++-mode-hook 'my:ac-c-header-init)
(add-hook 'c-mode-hook 'my:ac-c-header-init)

;;;;;;;;;;;;;;;;;;;;;;;;;
;; auto-complete-clang 
;;;;;;;;;;;;;;;;;;;;;;;;;
(require 'auto-complete-clang)
(setq ac-auto-start t)
(setq ac-quick-help-delay 0.5)
;; (ac-set-trigger-key "TAB")
;; (define-key ac-mode-map  [(control tab)] 'auto-complete)
(define-key ac-mode-map  [(control tab)] 'auto-complete)
(defun my-ac-config ()
  (setq ac-clang-flags
	(mapcar(lambda (item)(concat "-I" item))
	       (split-string
		"
/usr/lib/gcc/x86_64-redhat-linux/4.9.2/../../../../include/c++/4.9.2
/usr/lib/gcc/x86_64-redhat-linux/4.9.2/../../../../include/c++/4.9.2/x86_64-redhat-linux
/usr/lib/gcc/x86_64-redhat-linux/4.9.2/../../../../include/c++/4.9.2/backward
/usr/lib/gcc/x86_64-redhat-linux/4.9.2/include
/usr/local/include
/usr/include"
		)))
  (setq-default ac-sources '(ac-source-abbrev ac-source-dictionary ac-source-words-in-same-mode-buffers))
  (add-hook 'emacs-lisp-mode-hook 'ac-emacs-lisp-mode-setup)
  ;; (add-hook 'c-mode-common-hook 'ac-cc-mode-setup)
  (add-hook 'ruby-mode-hook 'ac-ruby-mode-setup)
  (add-hook 'css-mode-hook 'ac-css-mode-setup)
  (add-hook 'auto-complete-mode-hook 'ac-common-setup)
  (global-auto-complete-mode t))
(defun my-ac-cc-mode-setup ()
  (setq ac-sources (append '(ac-source-clang ac-source-yasnippet) ac-sources)))
(add-hook 'c-mode-common-hook 'my-ac-cc-mode-setup)
;; ac-source-gtags
(my-ac-config)
```

### 参考资料

- [C/C++ Development Environment for Emacs](http://tuhdo.github.io/c-ide.html#sec-1-3)
- [YouTube Vedio](https://www.youtube.com/watch?v=HTUE03LnaXA)


