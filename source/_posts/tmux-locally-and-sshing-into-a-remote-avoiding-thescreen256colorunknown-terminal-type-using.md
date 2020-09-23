---
title: Using tmux locally and SSHing into a remote: Avoiding the 'screen-256color': unknown terminal type. 
date: 2013-01-02 08:00:00
updated: 2015-11-22 13:44:01
tags: 
- mysql
- type
categories: 
- sql

---
set an alias in your .zshrc/.bashrc:

    alias ssh='TERM=xterm ssh'

