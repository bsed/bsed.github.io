---
title: "Compiling Vim with Python and Ruby support on Ubuntu"
categories: [ "Vim" ]
tags: [ "ubuntu","vim" ]
draft: false
slug: "vim-with-python-and-ruby-support-on-ubuntu-compiling"
date: "2011-10-24 21:16:00"
---

Check if Vim is compiled with Python or Ruby:

    λ vim --version | ack '(python|ruby)'

Remove Vim version installed with apt-get if present:

    λ sudo apt-get remove vim-common vim-runtime

Install dependencies needed to compile Vim:


<!--more-->


    λ sudo apt-get build-dep vim

Install Mercurial:

    λ sudo apt-get install mercurial

Clone Vim repository, compile it and install the new version:

    λ hg clone https://vim.googlecode.com/hg/ vim
    λ cd vim
    λ ./configure --enable-pythoninterp --enable-rubyinterp
    λ make
    λ sudo make install

