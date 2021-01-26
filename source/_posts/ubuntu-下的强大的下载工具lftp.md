---
title: "ubuntu 下的强大的下载工具lftp"
categories: [ "Linux" ]
tags: [ "ubuntu","lftp" ]
draft: false
slug: "ubuntu-under-the-powerful-download-tool-lftp"
date: "2013-03-19 15:13:00"
---

# 安装

    apt-get install lftp

# 登陆


<!--more-->


    lftp -p 22 -u root sftp://10.10.10.80
    Bookmark
    
    bookmark
    bookmark add
    open <bookmark>

# Mirror

上传当前目录：

    mirror -R .

上传时对于本地已删除的文件，也删除掉远程的：

    mirror -eR .

下载当前目录：
mirror .

上传目录时排除文件（比如.DS_Store）：
mirror -R -X .DS_Store .
