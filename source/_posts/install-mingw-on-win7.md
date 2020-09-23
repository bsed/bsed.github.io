---
title: 在win7上安装MinGW 4.8.1
date: 2011-09-08 21:40:00
updated: 2015-09-30 21:49:12
tags: 
- android
- 键盘
categories: 
- android

---
1、win7中环境变量设置位置：
    计算机>>>属性>>>高级系统设置>>>环境变量>>>用户变量>>>新建(或编辑)
2、添加环境变量(可根据需要添加)：
    1)  MinGW的文件安装位置：
         `MINGW_PATH=D:\CodeBlocks\MinGW`
    2)  BIN文件的安装位置：
         `PATH=%MINGW_PATH%\bin;%MINGW_PATH%\libexec\gcc\mingw32\4.8.1;%MINGW_PATH%\mingw32\bin`
    3)  库文件的安装位置：
         `LIBRARY_PATH=%MINGW_PATH%\lib;%MINGW_PATH%\lib\gcc\mingw32\4.8.1;%MINGW_PATH%\mingw32\lib`
    4)  头文件安装位置：
         `C_INCLUDE_PATH=%MINGW_PATH%\include;%MINGW_PATH%\include\ddk;%MINGW_PATH%\include\gdb;%MINGW_PATH%\include\gdiplus;%MINGW_PATH%\include\GL;%MINGW_PATH%\include\sys;%MINGW_PATH%\lib\gcc\mingw32\4.8.1\include
         CPLUS_INCLUDE_PATH=%MINGW_PATH%\lib\gcc\mingw32\4.8.1\include\c++;%MINGW_PATH%\lib\gcc\mingw32\4.8.1\include\c++\mingw32\bits;%MINGW_PATH%\lib\gcc\mingw32\4.8.1\include\c++\backward;%C_INCLUDE_PATH%`

（说明：1、以上以真实文件位置为准；2、关于CPLUS_INCLUDE_PATH的配置可能不全以后据需要添加）


<!--more-->


3、一些简单的测试：
    1)  在命令行（或DOS）模式中，运行gcc -v
    2)  编写简单测试程序test.c：
	
```
    #include <stdio.h>
    	void main()
    	{
    		printf("MinGW is OK！！");
    	}
```
然后在命令行（或DOS）模式中，
`gcc test.c -o test`
 进行测试：
`MinGW is OK！！`