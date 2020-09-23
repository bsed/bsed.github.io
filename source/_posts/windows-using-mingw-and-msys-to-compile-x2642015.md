---
title: windows下使用MinGW和msys编译x264[2015]
date: 2015-01-10 18:40:00
updated: 2015-01-13 09:08:50
tags: 
- EJB
- java-bean
categories: 
- java

---
## 1. 环境 ##

### 1.1 下载 mingw 和 MSYS ###

去[http://www.mingw.org/](http://www.mingw.org/)或去 [http://sourceforge.net/projects/mingw/files/](http://sourceforge.net/projects/mingw/files/)  下载

下载类似：[Mingw-get-setup.exe (86.5 kB)
](http://downloads.sourceforge.net/project/mingw/Installer/mingw-get-setup.exe?r=http%3A%2F%2Fsourceforge.net%2Fprojects%2Fmingw%2Ffiles%2FMinGW%2F&ts=1421059587&use_mirror=cznic) 的文件。

提示：msys此处就不用下载了，Mingw-get-setup.exe 中已经包含了msys1.0,后面安装的时候就可以看到该选项。

### 1.2 下载 yasm ###

x264编译里面的汇编代码需要 yasm.exe 

去官网：[http://yasm.tortall.net/Download.html](http://yasm.tortall.net/Download.html)  下载：[Win32.exe](http://www.tortall.net/projects/yasm/releases/yasm-1.3.0-win32.exe) (for general use on 32-bit Windows)
自己根据系统位数选择合适的版本
文件名为：`yasm-1.3.0-win32.exe`

### 1.3 下载 x264 ###

从官网[http://www.videolan.org/developers/x264.html](http://www.videolan.org/developers/x264.html)
解压到MSYS目录下home文件下或自己指定的文件夹下

### 1.4 下载 pthreadGC2.dll ###
pthreadgc2.dll是功能强大的处理数字化影视作品编辑软件premiere6运行所需的一个DLL文件。

## 2. 编译步骤 ##
### 2.1 安装 mingw 和 msys ###
执行下载文件：mingw-get-setup.exe，

    在第3步选择“Download latest repository catalogues”

    选择安装路径：C:\MinGW（我直接采用默认路径，未做修改）


在“Select Components”步骤中选择：

    √  C Compiler (默认且一定选择)

    √  C++ Compiler

    √  MSYS Basic System（选择此项将会安装msys，msys则不需要另外安装）

    √  MinGW Developer ToolKit

    等待安装完成。

安装完成后，在C:\MinGW目录下可以看到 msys 文件夹，msys也已经安装。

### 2.2 配置让x264编译时产生windows下调用dll对应的lib ###

> 为了方便VS200X以及VS2010调用x264的动态库，可以通过配置让x264编译时产生windows下调用dll对应的lib，当然，如果你没有这个需要，那么就可以省略以下处理，直接进入步骤
进入：C:\MinGW\msys\1.0\文件夹，使用UltraEdit打开msys.bat文件，在文件的最最前面加入如下一行：

    call "D:\Program Files\Microsoft Visual Studio 10.0\VC\bin\vcvars32.bat"

其中“D:\Program Files\Microsoft Visual Studio 10.0”为你机器上安装VS200X或VS2010的目录，我的电脑安装的是VS2010，并且安装在了D盘。文件修改后保存即可。

添加完成了以后，就可以运行下msys.bat这个文件了，然后键入`gcc   -v`，如果出现的不是什么 no command 和 no input file什么的，而是出现了一些版本的信息，证明MinGW安装对了。

### 2.3 配置 yasm ###

    为了减少配置环境变量的麻烦，本处直接把 yasm-1.3.0-win32.exe 文件名修改成：yasm.exe，并放到系统目录下：C:\WINDOWS\system32。或者 将 yasm-1.3.0-win32.exe 更名为 yasm.exe 后放到`C:\MinGW\bin` 目录下
### 2.4 编译x264 ###
双击“C:\MinGW\msys\1.0\msys.bat”，启动“MinGW32”，
切换到当前x264所在目录：`C:\MinGW\msys\1.0\home\x264`，
切换方法如下：

    $ cd /c 
    $ cd MinGW  
    $ cd msys   
    $ cd 1.0  
    $ cd home 
    $ cd x264

### 2.5 再执行以下命令：###

    //$ ./configure --enable-shared --disable-static --enable-memalign-hack
    $ ./configure --enable-shared
    $ make

请耐心等待一段时间$ make需要等待很长一段时间，等待编译结束。
为了汇总编译结果，请继续执行以下命令：

    $ make install

等待完成，在`C:\MinGW\msys\1.0\local`中将会生成 bin、include、lib等文件夹，其中包含了`x264.exe`、`libx264-142.dll`、lib和头文件等。
如图
![20150112191110.png][1]

附加：

用VC的lib工具导出lib

1. 下载pexports
[http://download.csdn.net/source/3036251](http://download.csdn.net/source/3036251)
把`pexports.exe`放到C:/Program Files/Microsoft Visual Studio 8/VC/bin

2. 导出lib库
```
    pexports libx264-116.dll > libx264-116.def
    lib /machine:ix86 /def:libx264-116.def
```
(在visual studio的命令行窗口中执行或者在CMD窗口先执行`C:\Program Files\Microsoft Visual Studio 8\VC\bin>vcvars32.bat`) 
步骤2 可以这样搞

在 C:\MinGW\msys\1.0\msys.bat 文件头部加上一条命令： 
`call "C:\Program Files\Microsoft Visual Studio 9.0\VC\bin\vcvars32.bat"`
使用make编译时，会自动生成lib文件

*编码命令:*
`x264 --profile baseline --crf 23 -o test.264 vga_640x480.yuv`

  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/2949383042.png