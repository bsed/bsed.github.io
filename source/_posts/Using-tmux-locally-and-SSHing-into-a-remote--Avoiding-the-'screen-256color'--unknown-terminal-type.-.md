---
title: "Using tmux locally and SSHing into a remote: Avoiding the 'screen-256color': unknown terminal type. "
categories: [ "Linux" ]
tags: [ "term" ]
draft: false
slug: "tmux-locally-and-sshing-into-a-remote-avoiding-thescreen256colorunknown-terminal-type-using"
date: "2013-01-02 08:00:00"
---

set an alias in your .zshrc/.bashrc:

    alias ssh='TERM=xterm ssh'

