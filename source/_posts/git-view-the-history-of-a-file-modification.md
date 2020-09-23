---
title: git 查看某文件的修改历史
date: 2018-04-27 14:40:00
updated: 2018-05-15 12:26:58
tags: 
- debian
- nginx
- docker
categories: 
- linux

---
前提 先进入此文件所在的目录下 

 - 1.`git log filename` 可以看到fileName相关的commit记录
 - 2.`git log -p filename` 可以显示每次提交的diff
 - 3.只看某次提交中的某个文件变化，可以直接加上 fileName `git show commit-id filename`
 - 4.根据commit-id查看某个提交 `git show -s --pretty=raw ca53e82`
 - 5.借助可视化工具 如 sourceTree 在最后一次修改的记录上 右键选中文件 查看历史修改
 - 6.git log 的常用选项
<!--more-->

 
| 选项              | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| `-p`              | 按补丁格式显示每个更新之间的差异。                           |
| `--stat`          | 显示每次更新的文件修改统计信息。                             |
| `--shortstat`     | 只显示 –stat 中最后的行数修改添加移除统计。                  |
| `--name-only`     | 仅在提交信息后显示已修改的文件清单。                         |
| `--name-status`   | 显示新增、修改、删除的文件清单。                             |
| `--abbrev-commit` | 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。            |
| `--relative-date` | 使用较短的相对时间显示（比如，“2 weeks ago”）。              |
| `--graph`         | 显示 ASCII 图形表示的分支合并历史。                          |
| `--pretty`        | 使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。 |

 

| 选项                  | 说明                               |
| --------------------- | ---------------------------------- |
| `-(n)`                | 仅显示最近的 n 条提交              |
| `--since`, `--after`  | 仅显示指定时间之后的提交。         |
| `--until`, `--before` | 仅显示指定时间之前的提交。         |
| `--author`            | 仅显示指定作者相关的提交。         |
| `--committer`         | 仅显示指定提交者相关的提交。       |
| `--grep`              | 仅显示含指定关键字的提交           |
| `-S`                  | 仅显示添加或移除了某个关键字的提交 |

参考： [https://github.com/geeeeeeeeek/git-recipes/wiki/5.3-Git-log-%E9%AB%98%E7%BA%A7%E7%94%A8%E6%B3%95](https://github.com/geeeeeeeeek/git-recipes/wiki/5.3-Git-log-%E9%AB%98%E7%BA%A7%E7%94%A8%E6%B3%95)