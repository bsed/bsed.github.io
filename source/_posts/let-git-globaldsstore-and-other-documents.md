---
title: 让 Git 全局性的忽略 .DS_Store等文件
date: 2015-10-23 21:13:00
updated: 2016-01-24 08:28:05
tags: 
- javascript
- useragent
categories: 
- js

---
Mac 中每个目录都会有个文件叫.DS_Store, 用于存储当前文件夹的一些 Meta 信息。每次提交代码时，我都要在代码仓库的 .gitignore 中声明，忽略这类文件。有方法可以全局性的忽略某种类型的文件吗？

按照以下两步就可实现

创建 `~/.gitignore_global` 文件，把需要全局忽略的文件类型塞到这个文件里。


<!--more-->


    # .gitignore_global
    ####################################
    ######## OS generated files ########
    ####################################
    .DS_Store
    .DS_Store?
    *.swp
    ._*
    .Spotlight-V100
    .Trashes
    Icon?
    ehthumbs.db
    Thumbs.db
    ####################################
    ############# Packages #############
    ####################################
    *.7z
    *.dmg
    *.gz
    *.iso
    *.jar
    *.rar
    *.tar
    *.zip

创建`.gitconfig` 文件在 `~/.gitconfig` 中引入 `.gitignore_global`。

这是我的.gitconfig 文件:

    [core]
      # Global .gitignore file
      excludesfile = ~/.gitignore_global
    
    [user]
      # TODO
      name = xxx
      email = 284178359@qq.com
    
    [color]
      status = auto
      branch = auto
      diff = auto
      interactive = auto
    
    [branch]
      # auto-adds the --track flag to git branch. Helps out arc (for Phabricator).
      autosetupmerge = always
    
    [alias]
      graph = log --graph --decorate --oneline --pretty=format:"%h%x20%Cgreen%d%x20%Cred%an%x20%Cblue%ad%x20%Creset%s" --all --full-history --date=short
      ls-ignored = ls-files --exclude-standard --ignored --others
      co = checkout
      st = status
      ci = commit
      br = branch
      df = diff
      dfs = diff --staged
      lp = log -p
    
      # Ignore whitespace changes
      bw = blame -w
    
    [merge]
      # Always show a diffstat at the end of a merge
      stat = true
    
    [diff]
      # Use mnemonic prefixes (index, work tree, commit, object) instead of the standard a and b notation
      mnemonicprefix = true
    
      # Detect renames as well as copies
      renames = copies

搞定了！在所有的文件夹下 .DS_Store .swp .zip 等文件类型会被 Git 自动忽略。
![yigrherb__gitignore_ds_store.png][1]

**参考资料:**

 1. [Github: Create a global
    .gitignore](https://help.github.com/articles/ignoring-files/)
 2. [What is .DS_Store
    file?](http://osxdaily.com/2009/12/31/what-is-a-ds_store-file/)
 3. [.gitignore all the .DS_Store files in every folder and
    subfolder](http://stackoverflow.com/questions/18393498/gitignore-all-the-ds-store-files-in-every-folder-and-subfolder)


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/1902407017.png