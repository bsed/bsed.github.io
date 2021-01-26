---
title: "Emacs 配置C++ IDE 开发环境"
categories: [ "Linux" ]
tags: [ "emacs" ]
draft: false
slug: "emacs-build-c-ide"
date: "2017-04-04 12:45:00"
---

该解决方案适用于Linux/OSX/Cygwin (应该也适用于Windows环境,但我不在Windows下作开发所以不确定).

只有很少量的配置. 你只需要安装 `GNU Global` 以及另外两个Emacs插件:

- [company](https://github.com/company-mode/company-mode) ,用于代码补全
- [emacs-helm-gtags](https://github.com/syohex/emacs-helm-gtags) 或 [emacs-counsel-gtags](https://github.com/syohex/emacs-counsel-gtags) 用于代码导航. 我个人用的是 `emacs-counsel-gtags`.

下面是步骤说明.


<!--more-->


# 步骤1, 创建一个示例项目来作实验

假设我有两个目录 `~/proj1` 以及 `~/proj2`. 两个项目都会用到第三方库的C++头文件,这些头文件放在只读目录 `/usr/include`中.

新建一个 `~/obj` 目录来存放第三方库的索引文件.

```
mkdir -p ~/{proj1,proj2,obj}
```

`~/proj2/lib.cpp` 的内容为:

```
void proj2_hello(int a2, char* b2) {
}
```

`~/proj1/main.cpp` 的内容为:

```
void proj1_hello(int a1, char* b1) {
}

int main(int argc, char *argv[]) {
  return 0;
}
```

# 步骤2, 扫描C++代码并配置Emacs

在shell中运行下列代码来扫描代码Run below command in shell to scan code,

```
cd /usr/include && MAKEOBJDIRPREFIX=~/obj gtags -O 
cd /usr/src/linux-headers-4.10.0-15/include && MAKEOBJDIRPREFIX=~/obj gtags -O 
cd ~/proj1 && gtags && cd ~/proj2 && gtags
```

设置好相应的Emacs插件后(最简单的方法只需要把这些插件安装好就行了), 将下列代码插入到 `~/.emacs` 中,

```
;; Please note `file-truename' must be used!
(setenv "GTAGSLIBPATH" (concat "/usr/include"
                               ":"
                               (file-truename "~/proj2")
                               ":"
                               (file-truename "~/proj1")))
(setenv "MAKEOBJDIRPREFIX" (file-truename "~/obj/"))
(setq company-backends '((company-dabbrev-code company-gtags)))
```

# 使用方法

就像平常那样使用Emacs就行了.

不过有一点要逐一,你需要安装8月25日之后编译的company,因为我昨天才[修复了一个company的issue](https://github.com/company-mode/company-mode/issues/570).

![emacs_cpp_ide.png][1]
# 技术细节(选读)

通过查阅 [GNU Global manual](https://www.gnu.org/software/global/manual/global.html) 来搞清楚环境变量 `GTAGSLIBPATH` 和 `MAKEOBJDIRPREFIX` 的意思.

参考:

 - http://blog.binchen.org/posts/emacs-as-c-ide-easy-way.html
 - https://www.hiroom2.com/2016/10/31/emacs-gtags-package/
 - https://github.com/lujun9972/emacs-document/blob/master/emacs-common/%E7%AE%80%E5%8D%95%E5%87%A0%E6%AD%A5%E5%B0%86Emacs%E6%89%93%E9%80%A0%E6%88%90%E4%B8%BAC%2B%2B_IDE.org
 - http://emacser.com/emacs-cpp-dev.htm

  [1]: https://imgs.gnux.cn/usr/uploads/2017/04/3196100774.png