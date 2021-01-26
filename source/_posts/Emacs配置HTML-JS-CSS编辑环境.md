---
title: "Emacs配置HTML/JS/CSS编辑环境"
categories: [ "Vim" ]
tags: [ "emacs" ]
draft: false
slug: "emacs-configuration-htmljscss-editing-environment"
date: "2017-04-03 09:35:00"
---

## 1. web-mode

[web-mode](http://web-mode.org/) 提供了很好的wen配件（HTML、JavaScript、CSS、PHP等）的代码缩进、折叠和高亮等出色的功能。

有用技巧：

- `C-c C-n`：放在HTML标签上，在标签间跳转。
- `C-c C-f`：放在HTML标签上，在标签折叠。

## 2. emmet-mode

[emmet-mode](https://github.com/smihica/emmet-mode)是[emmet](http://emmet.io/)的Emacs扩展，取代了陈旧的[ZenCoding](http://www.emacswiki.org/emacs/ZenCoding)。只需要输入制定的缩写，这个工具可以自动补全HTML标签。非常方便。

有用技巧：

- `M-x emmet-mode`：打开emmet-mode。
- `C-j`：自动补全。


<!--more-->


补全缩写规律：

- `a`：a+href
- `#q`：div+id(q)
- `.x`：div+class(x)
- `#q.x`：div+id(q)+class(x)

## 附加内容

Emacs编程环境设置

Emacs web config
```
;;;;;;;;;;;;;;
;emmet-mode
;;;;;;;;;;;;;
(require 'emmet-mode)
(add-hook 'sgml-mode-hook 'emmet-mode) ;; Auto-start on any markup modes
(add-hook 'html-mode-hook 'emmet-mode)
(add-hook 'web-mode-hook 'emmet-mode)
(add-hook 'css-mode-hook  'emmet-mode)


;;;;;;;;;;;;;;
;web-mode
;;;;;;;;;;;;;;;
(require 'web-mode)
(add-to-list 'auto-mode-alist '("\\.phtml\\'" . web-mode))
(add-to-list 'auto-mode-alist '("\\.tpl\\.php\\'" . web-mode))
(add-to-list 'auto-mode-alist '("\\.[agj]sp\\'" . web-mode))
(add-to-list 'auto-mode-alist '("\\.as[cp]x\\'" . web-mode))
(add-to-list 'auto-mode-alist '("\\.erb\\'" . web-mode))
(add-to-list 'auto-mode-alist '("\\.mustache\\'" . web-mode))
(add-to-list 'auto-mode-alist '("\\.djhtml\\'" . web-mode))
(add-to-list 'auto-mode-alist '("\\.html?\\'" . web-mode))
(defun my-web-mode-hook ()
  "Hooks for Web mode."
  (setq web-mode-markup-indent-offset 2)
)
(add-hook 'web-mode-hook  'my-web-mode-hook)
```


### 参考资料

- [web-mode说明文档](http://web-mode.org/)