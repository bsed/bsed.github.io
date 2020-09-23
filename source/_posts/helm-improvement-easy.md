---
title: Easy helm improvement
date: 2014-08-11 10:10:00
updated: 2015-11-22 10:11:56
tags: 
- linux
- useradd
categories: 
- linux

---
When you press <kdb>DEL</kdb> (also known as backspace) in a `helm` buffer, and there isn't any input to delete, it only errors at you with:

Text is read only
Why not make it do something useful instead, for instance close `helm`?


<!--more-->


Easy:

    (require 'helm)
    (defun helm-backspace ()
      "Forward to `backward-delete-char'.
    On error (read-only), quit without selecting."
      (interactive)
      (condition-case nil
          (backward-delete-char 1)
        (error
         (helm-keyboard-quit))))
    
    (define-key helm-map (kbd "DEL") 'helm-backspace)