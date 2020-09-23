---
title: 使用el-get管理emacs包
date: 2013-09-26 21:05:00
updated: 2015-07-08 21:05:45
tags: 
- golang
- 学习笔记
categories: 
- go

---
[el-get]()是emacs的一个包管理器。你可以把它当作是emacs的synaptic、yum或者pacman，功能强大，用起来也十分方便。通过它你可以安装和管理各种elisp的包。对于我来说，el-get最大的功能在于，当你有多台机器时，仅仅需要一个.emacs文件就可以保证多个机器上的elisp包一致，而不是每个机器都需要手动管理。想想看，没有el-get的话，当你重装系统之后需要花多少时间来安装各种elisp包吧。有了el-get之后，只要一个.emacs文件，当你第一次打开emacs的时候，各种包已经开始默默的下载和安装了，完全不用你操心。这是一件多么美好的事啊。

el-get对软件包的描述放在recipe文件中，包括安装方式、url、安装完之后的操作，以及软件包的基本配置。也就是说当你用el-get安装完某个包之后，自己的.emacs文件基本上不用修改，新的包就可以用了。el-get支持多种安装方式，可以直接从github clone，也可以从指定的url下载回来然后解压，另外还支持ELPA，cvs，bzr，apt-get，pacman等等。最强大的地方在于你可以自己定义[recipe](https://github.com/dimitri/el-get/tree/master/recipes)，如果默认的recipe文件中没有你需要的包，那么你完全可以自己写一个。如果你在github上面fork了el-get，可以给作者提交pull request，请求将自己定义的recipe加入默认的recipe中。这样如果下次有别人需要用到这个包，你的recipe就派上用场了。


<!--more-->


安装el-get非常简单，只要打开你的emacs，切换到scratch buffer，复制下面这段代码，然后在最后一个括号后面C-j就可以了。

    (url-retrieve
     "https://raw.github.com/dimitri/el-get/master/el-get-install.el"
     (lambda (s)
       (let (el-get-master-branch)
         (goto-char (point-max))
         (eval-print-last-sexp))))

安装好el-get后，配置文件里面加入下面的内容

    (setq
     el-get-sources
     '((:name asciidoc
              :type elpa
              :after (progn
                       (autoload 'doc-mode "doc-mode" nil t)
                       (add-to-list 'auto-mode-alist '("\.adoc$" . doc-mode))
                       (add-hook 'doc-mode-hook '(progn
                                                   (turn-on-auto-fill)
                                                   (require 'asciidoc)))))
    
       (:name buffer-move   ; have to add your own keys
               :after (progn
                        (global-set-key (kbd "<C-S-up>") 'buf-move-up)
                        (global-set-key (kbd "<C-S-down>") 'buf-move-down)
                        (global-set-key (kbd "<C-S-left>") 'buf-move-left)
                        (global-set-key (kbd "<C-S-right>") 'buf-move-right)))
    
       (:name smex  ; a better (ido like) M-x
              :after (progn
                       (setq smex-save-file "~/.emacs.d/.smex-items")
                       (global-set-key (kbd "M-x") 'smex)
                       (global-set-key (kbd "M-X") 'smex-major-mode-commands)))
    
       (:name lisppaste        :type elpa)))
    
    (setq my-packages
          (append
           '(cssh el-get switch-window escreen vkill xcscope color-theme color-theme-railscasts color-theme-tomorrow yasnippet python-mode python-pep8 pymacs rope ropemacs ropemode pylookup haskell-mode anything helm emacs-w3m auto-complete browse-kill-ring sr-speedbar session popup markdown-mode xml-rpc-el)
           (mapcar 'el-get-source-name el-get-sources)))
    
    (el-get 'sync my-packages)

上面的el-get-sources变量是自定义的recipe，my-packages则是需要安装的包。只要把包的名字写在my-packages里面，下次启动emacs的时候el-get就会自动安装。

使用el-get就是这么简单。但是在使用的过程中发现el-get还有些不完美的地方。比如用el-get安装color-theme这个插件的时候，总会提示下载的color-theme.tar.gz无法解压。经过一番调查发现原来是el-get的http-method有问题。el-get下载的color-theme.tar.gz总会比用wget下载的少几个字节。http-method的实现是先用url-retrieve把文件拉到当前的buffer中，再用write-file将当前buffer的内容写入文件。问题就出在write-file这里。write-file默认会对buffer进行一些编码的转换，结果导致丢掉了几个字节。修改的方法也很简单，只要将write-file替换为write-region即可。发现这个bug之后在github上给作者提交了[pull request](https://github.com/dimitri/el-get/pull/1156)，已经被合并了。之后再有人用el-get就不会碰到这个问题了。
