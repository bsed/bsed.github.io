---
title: "node版本管理工具nvm"
categories: [ "JS" ]
tags: [ "nodejs","nvm" ]
draft: false
slug: "node-version-management-tool-nvm"
date: "2014-01-20 11:29:00"
---

## 使用Homebrew安裝nvm

Homebrew基本上已經是MAC user必備的tool了，還沒裝的人強烈建議趕快裝上它吧！網路上教學很多，這邊就不再多介紹了。

首先先用$ brew install nvm安裝nvm：
```
 $ brew install nvm
==> Downloading https://github.com/creationix/nvm/archive/v0.2.0.tar.gz
######################################################################## 100.0%

==> Caveats
Add the following to $HOME/.bashrc, $HOME/.zshrc, or your shell's equivalent configuration file:

  source $(brew --prefix nvm)/nvm.sh

Type `nvm help` for further information.
==> Summary
  /usr/local/Cellar/nvm/0.2.0: 3 files, 24K, built in 5 seconds
```
安裝完後，為了讓你可以直接在shell使用nvm指令，必須在你的 .bash_profile 加入以下這行（習慣把設定放在.bashrc的人可以把以下的.bash_profile改成.bashrc）
```bash
source $(brew --prefix nvm)/nvm.sh
```
或者直接輸入以下這行來加入
```bash
 $ echo "source $(brew --prefix nvm)/nvm.sh" >> .bash_profile
```
記得重新source你的 `.bash_profile` 來讓設定生效
```bash
 $ . ~/.bash_profile
```
OK，以上就完成了nvm的安裝，簡單吧！

## 使用nvm安裝Node.js


<!--more-->


安裝完了nvm，接著安裝主角node。先用$ nvm ls-remote指令看一下有哪些版本可以安裝：
```bash
 $ nvm ls-remote
      .
      .
      .
  v0.10.20
  v0.10.21
  v0.10.22
  v0.10.23
  v0.10.24
   v0.11.0
   v0.11.1
   v0.11.2
   v0.11.3
   v0.11.4
   v0.11.5
   v0.11.6
   v0.11.7
   v0.11.8
   v0.11.9
  v0.11.10
```
真夭壽多啊..果然是正在火速成長中的技術！

直接用`nvm install <version>`指令安裝官網上建議的版本：
```bash
 $ nvm install v0.10.24
######################################################################## 100.0%

Now using node v0.10.24
```
也同時安裝最新版來測試nvm的版本管理功能：
```bash
 $ nvm install v0.11.10
######################################################################## 100.0%

Now using node v0.11.10
```
## 使用nvm無痛切換Node.js版本

在介紹使用nvm切換版本前，先簡單說明nvm的工作原理。

nvm會把各個版本的node安裝在/usr/local/opt/nvm底下。我們可以看看該目錄底下放了哪些東西：
```
 $ ls /usr/local/opt/nvm
INSTALL_RECEIPT.json  LICENSE.md  alias  bin  nvm.sh  v0.10.24  v0.11.10
```
我們可以發現透過nvm安裝這兩個版本，事實上會在nvm目錄下另外建立了v0.10.24以及v0.11.10兩個目錄來分別存放node的binary檔。又nvm會在你的$PATH最前面安插指定版本的目錄，透過這個方式你在使用node指令時就會用指定的版本來運作了。

實際確認PATH的值看看：
```bash
 $ echo $PATH
/usr/local/opt/nvm/v0.11.10/bin: ...
```
nvm的用法非常的簡單。我們可以另外用$ nvm ls指令確認nvm目前可以管理的版本有哪些：
```bash
 $ nvm ls

  v0.10.24
  v0.11.10
current:    v0.11.10
```
由於透過nvm安裝node，會自動把最後安裝的版本設為目前使用中的版本，因此上述指令會看到current: v0.11.10，表示我們目前正在使用v0.11.10

我們可以用$ nvm use <version>切換版本：
```
 $ nvm use v0.10.24
```
Now using node v0.10.24
也可以偷懶一點，不用打完整的版號：
```
 $ nvm use 0.10
Now using node v0.10.24
```
## 切換成別的版本看看：
```bash
 $ nvm use 0.11
Now using node v0.11.10
```
夠簡單吧！

不過問題來了，如果你另外開一個shell視窗，並輸入nvm，會發現current version是空的：
```bash
 $ nvm ls

  v0.10.24
  v0.11.10
current: 
```
這是因為利用nvm use指令只會在當前的shell生效，當你開了新的shell就會發現$PATH的值已經不包含剛才設定的node目錄了。要解決這個問題就是利用$ nvm alias default <version>來設定一個預設的node版本：
```bash
 $ nvm alias default 0.10
default -> 0.10 (-> v0.10.24)
```
此時再打開另一個shell視窗，就可以直接使用你所設定的node版本了。

## 實際跑跑看Node.js

我們直接拿官網的例子來試試看
先產生一個example.js的檔案：
```
 $ touch example.js
```
內容如下:

```javascript
 http = require('http');
http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello World\n');
}).listen(1337, '127.0.0.1');
console.log('Server running at http://127.0.0.1:1337/');
```
執行看看
```bash
 $ node example.js
```
Server running at http://127.0.0.1:1337/
打開瀏覽器，輸入http://127.0.0.1:1337，如果看到 "Hello Word" 就代表成功了