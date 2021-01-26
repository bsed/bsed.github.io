---
title: " linux新建和删除畸形文件和文件夹"
categories: [ "Linux" ]
tags: [ "linux","rm -rf" ]
draft: false
slug: "linux-to-create-and-delete-the-abnormal-files-and-folders"
date: "2013-11-13 08:34:00"
---

总结下我在linux下遇到的畸形文件或文件夹的新建和删除方法

## 1.文件或文件夹中含空格和"\"的
  例如：  "a b" "\a" "=a"这样的文件或者目录
新建：

    tocuh a\ b  tocuh  \\a  touch \=a

删除：
这类删除是比较简单的，只要用"\"来转义就可以了，一般用TAB补全也可以


<!--more-->


    rm -rf a\ b   rm -rf \\a  rm -rf \=a

还有一种方法就是把整个文件名用""括起来就能直接删除了

    rm -rf "a b" "\a" "=a"


## 2.文件或文件夹名字中有 --

  例如  文件叫 --help ，由于在linux下"-"后面就是命令参数，如果出现在文件名中后面的内容会执照命令的参数来执行。所以不能用正常的方法创建和删除。
新建：

    touch  -- --help，mkdir -- -

删除：
这类的文件或目录 直接 rm -rf --help 或者用 "\"来转义是会报错的,我们可以使用“--”符号来解决这个问题

     rm  -- --help  

## 3.乱码文件或文件夹

这类文件或文件夹出现多是由于解压压缩包产生的

删除方法
由于linux下文件的i节点是唯一的，所以可以通过i节点来删除，这种方法是通用方法，同时适合上面两种情况。
先`ls -li` 查看文件的innode节点号

    7605159 -rw-r--r-- 1 root root 0 2013-07-23 14:45 aa

之后

    find . -inum 7605159  -exec rm -rf {} \; 

或者

    find . -type f -inum 7605159 -delete


