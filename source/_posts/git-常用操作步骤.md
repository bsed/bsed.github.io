---
title: "git 常用操作步骤"
categories: [ "Linux" ]
tags: [ "git" ]
draft: false
slug: "git-operation-specification"
date: "2018-04-18 09:17:00"
---

## git 操作规范

### 一、 创建与合并分支

**1、 从master分支创建dev分支并切换到dev分支**

```
git checkout master

git checkout -b dev
```


<!--more-->


其中，git checkout -b dev 等价于:

```
git branch dev

git checkout dev
```

（1）

```
git branch
```

查看本地当前的分支，分支前面带“*”表示当前分支，剩下的分支表示本地有的分支。

（2）

```
git branch -a
```

查看远程全部的分支，白色的表示本地有的，红色的表示本地没有，仅在远程存在。

**2、修改代码、提交代码（当前的操作是在dev分支上进行）**

```
git add a.html

git commit -m "提交文件a.html"
```

**3、分支合并(将dev合并到master)**

```
git checkout master 

git merge dev
```

**4、合并完成后，删除dev分支.(删除dev分支时，注意我们当前所在的分支不能是dev分支)**

```
git branch -d dev
```

**5、删除后，查看分支(此时看不到dev分支了)**

```
git branch
```

**6、总结 ：工作中经常从master创建新的分支，具体操作如下**

```
master创建新分支：

git checkout master

git checkout -b  issues1234

git push origin issues1234

git add ..

git commit -m "***"

git push origin issues1234
```

> 注意：将本地分支branch1推到远端的branch2操作步骤：

```
    git push origin branch1:branch2
```

**7、删除分支：**

```
git branch -D   issues1234  //本地强制删除分支issues1234

git push origin  :issues1234  //推到远程
```

### 二、 解决冲突

**1、发生冲突的文件**

```
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```

其中，git使用<<<<<<<，=======，>>>>>>>标记文件中自己和别人产生冲突的部分。

在<<<<<<<，=======之间为自己的代码；
=======，>>>>>>>之间为别人的代码。

如果保留自己的代码，将别人的代码删掉即可。

**2、冲突解决后提交**

```
git status

git add ***

git commit -m "fix conflict"

git push origin 分支名
```

### 三、Bug分支

**1、储藏更改:将当前更改的代码储藏起来，等以后恢复使用**

```
git stash
```

**2、恢复储藏的代码**

```
git stash pop //恢复的同时把stash内容删掉

或者

git stash apply  //恢复stash，但是stash内容并不删除

git stash drop //在上面操作的基础上，以此来删除stash


注： git stash list //查看全部的stash列表
```

**3、将stash空间清空**

```
git stash clear
```

**4、git stash pop 和 git stash apply 区别**

```
原来git stash pop stash@{id}命令会在执行后将对应的stash id 从stash list里删除，
而 git stash apply stash@{id} 命令则会继续保存stash id。
```

### 四、版本回退

**1、回退至上一个版本**

```
git reset --hard HEAD 
```

**2、回退至指定版本**

```
git reset --hard  版本号
```

**3、查看以往版本号(本地的commit)**

```
git reflog
```

**4、查看各版本号及信息(所有的commit：本地commit + 其他同事的commit)**

```
git log
```

### 五、撤销修改

**1、撤销修改**

```
git  checkout -- a.html
```

> 分两种情况分析：

```
①： 还没有执行 git add 操作，执行上面的操作后，会恢复到和版本库中一模一样的版本状态。

②： 执行了git add ，还没执行 git commit ,再执行上面的操作后，会恢复到git add 结束后的状态
```

注：一旦执行了git commit -m "***"，就不能再使用上面的命令回退。

### 六、对于已经push的版本，进行回退

**1、第一步：**

```
git reset --hard 版本号 //本地回退到指定的版本
```

**2、第二步：**

```
git push  -f origin dev    //将远程的也回退到指定版本
```

### 七、本地同步远程删除的分支

```
git fetch origin -p  //用来清除已经没有远程信息的分支，这样git branch -a 就不会拉取远程已经删除的分支了
```

### 八、删除掉没有与远程分支对应的本地分支

从gitlab上看不到的分支在本地可以通过git branch -a 查到，删掉没有与远程分支对应的本地分支：

```
git fetch -p
```

### 九、查看远程库的一些信息，及与本地分支的信息

```
 git remote show origin 
```

### 十、git stash临时保存本地操作

1、使用'git stash'就可以将你当前未提交到本地（和服务器）的代码推入到Git的栈中，这时候你的工作区间和上一次提交的内容是完全一样的.

2、再切换到别的分支改紧急bug.

3、改完后，切到刚才的分支，使用'git stash apply'将以前一半的工作应用回来。

也许有的人会说，那我可不可以多次将未提交的代码压入到栈中？答案是可以的。当你多次使用'git stash'命令后，你的栈里将充满了未提交的代码，这时候你会对将哪个版本应用回来有些困惑，'git stash list'命令可以将当前的Git栈信息打印出来，你只需要将找到对应的版本号，例如使用'git stash apply stash@{1}'就可以将你指定版本号为stash@{1}的工作取出来，当你将所有的栈都应用回来的时候，可以使用'git stash clear'来将栈清空。
在这里顺便提下git format-patch -n , n是具体某个数字， 例如 'git format-patch -1' 这时便会根据log生成一个对应的补丁，如果 'git format-patch -2' 那么便会生成2个补丁，当然前提是你的log上有至少有两个记录。

原文：[https://segmentfault.com/a/1190000014461898?utm_source=index-hottest](https://segmentfault.com/a/1190000014461898?utm_source=index-hottest)