---
title: "Win8下Android SDK环境变量安装"
categories: [ "Java" ]
tags: [ "android" ]
draft: false
slug: "android-sdk-win8-environment-variable-installation"
date: "2013-10-01 09:00:00"
---

## 一：下载 Android SDK

http://developer.android.com/sdk/index.html

这里有两种，一种是下载“ADT Bundle for Windows” ，这是个已经集成好的Eclipse开发环境，已经配置好所有的东西，只需要做安装JDK和配置环境变量。

还有一种就是自己有Eclipse只是想单独下载Android SDK的。这种情况请选择“USE AN EXISTING IDE”，下载SDK Tools For Windows。

## 二：安装Android SDK

这里我解压下载的：ADT Bundle for Windows文件得到：adt-bundle-windows-x86_64-20130522目录

选择好安装目录，一路下一步就行了，注意：如果你没有配置JAVA_HOME环境变量，Android SDK安装检测可能会报错。

配置Android SDK环境变量，这个是一个很容易出错的地方，如果只是下载了“ADT Bundle for Windows”，没有配置这个环境变量的话，那么在启动虚拟机AVD的时候可能会出错。


<!--more-->


ANDROID_SDK_HOME：设置你Android SDK的安装目录就行了。我的是：D:\Android\android-sdk

PATH：把“%ANDROID_SDK_HOME%\tools;%ANDROID_SDK_HOME%\platform-tools;” 添加到系统环境变量path下。

## 三：在adt-bundle-windows-x86_64-20130522目录下运行SDK Manager.exe

发现：

WIN8下的Android SDK 秒退了
解决办法： 找到Android SDK的安装目录下 android-sdk-windows/tools/lib/find_java.bat批处理文件，备份一下。（随便重命名，更改后缀格式）。 
然后新建同名格式文件，将以下内容粘贴复制，保存即可。 
然后，启动 SDK Manager ，可以打开窗口。
把find_java.bat中全部替换成下面的：

    @echo off
    rem Copyright (C) 2007 The Android Open Source Project
    rem
    rem Licensed under the Apache License, Version 2.0 (the "License");
    rem you may not use this file except in compliance with the License.
    rem You may obtain a copy of the License at
    rem
    rem      http://www.apache.org/licenses/LICENSE-2.0
    rem
    rem Unless required by applicable law or agreed to in writing, software
    rem distributed under the License is distributed on an "AS IS" BASIS,
    rem WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    rem See the License for the specific language governing permissions and
    rem limitations under the License.
     
    rem This script is called by the other batch files to find a suitable Java.exe
    rem to use. The script changes the "java_exe" env variable. The variable
    rem is left unset if Java.exe was not found.
     
    rem Useful links:
    rem Command-line reference:
    rem   http://technet.microsoft.com/en-us/library/bb490890.aspx
     
    rem Check we have a valid Java.exe in the path. The return code will
    rem be 0 if the command worked or 9009 if the exec failed (program not found).
    rem Java itself will return 1 if the argument is not understood.
    set java_exe=java.exe
    rem search it in the path and verify we can execute it
    for %%a in (%java_exe%) do set java_exe=%%~s$PATH:a
    if not exist %java_exe% goto SearchForJava
    %java_exe% -version 2>nul
    if ERRORLEVEL 1 goto SearchForJava
    goto :SearchJavaW
     
     
    rem ---------------
    :SearchForJava
    rem We get here if the default %java_exe% was not found in the path.
    rem Search for an alternative in %ProgramFiles%\Java\*\bin\java.exe
     
    echo.
    echo WARNING: Java not found in your path.
     
    rem The strategy is to look for Java under these 3 locations:
    rem - %ProgramFiles%, which may point to either a 32-bit or 64-bit install
    rem                   depending on the current invocation context
    rem - %ProgramW6432%, which points to a 32-bit install. This may not be defined.
    rem - %ProgramFiles(x86)%, which points to a 64-bit install. This may not be defined.
     
    if not defined ProgramFiles goto :Check64
    echo Checking if Java is installed in %ProgramFiles%\Java.
     
    set java_exe=
    for /D %%a in ( "%ProgramW6432%\Java\*" ) do call :TestJavaDir "%%a"
    if defined java_exe goto :SearchJavaW
     
    rem Check for the "default" 64-bit version if it's not the same path
    :Check64
    if not defined ProgramW6432 goto :Check32
    if "%ProgramW6432%"=="%ProgramFiles%" goto :Check32
    echo Checking if Java is installed in %ProgramW6432%\Java instead (64-bit).
     
    set java_exe=
    for /D %%a in ( "%ProgramW6432%\Java\*" ) do call :TestJavaDir "%%a"
    if defined java_exe goto :SearchJavaW
     
    rem Check for the "default" 32-bit version if it's not the same path
    :Check32
    if not defined ProgramFiles(x86) goto :CheckFailed
    if "%ProgramFiles(x86)%"=="%ProgramFiles%" goto :CheckFailed
    echo Checking if Java is installed in %ProgramFiles(x86)%\Java instead (32-bit).
     
    set java_exe=
    for /D %%a in ( "%ProgramFiles(x86)%\Java\*" ) do call :TestJavaDir "%%a"
    if defined java_exe goto :SearchJavaW
     
    :CheckFailed
    echo.
    echo ERROR: No suitable Java found. In order to properly use the Android Developer
    echo Tools, you need a suitable version of Java JDK installed on your system.
    echo We recommend that you install the JDK version of JavaSE, available here:
    echo   http://www.oracle.com/technetwork/java/javase/downloads
    echo.
    echo You can find the complete Android SDK requirements here:
    echo   http://developer.android.com/sdk/requirements.html
    echo.
    goto :EOF
     
    rem ---------------
    :TestJavaDir
    rem This is a "subrountine" for the for /D above. It tests the short version
    rem of the %1 path (i.e. the path with only short names and no spaces).
    rem However we use the full version without quotes (e.g. %~1) for pretty print.
    if defined java_exe goto :EOF
    set full_path=%~1\bin\java.exe
    set short_path=%~s1\bin\java.exe
     
    %short_path% -version 2>nul
    if ERRORLEVEL 1 goto :EOF
    set java_exe=%short_path%
     
    echo.
    echo Java was found at %full_path%.
    echo Please consider adding it to your path:
    echo - Under Windows XP, open Control Panel / System / Advanced / Environment Variables
    echo - Under Windows Vista or Windows 7, open Control Panel / System / Advanced System Settings / Environment Variables
    echo At the end of the "Path" entry in "User variables", add the following:
    echo   ;%full_path%
    echo.
    goto :EOF
     
    rem ---------------
    :SearchJavaW
    rem Called once java_exe has been set. Try to see if we can find a javaw
    rem to use. If not, we'll default to using java_exe.
    for %%a in (%java_exe%) do set p=%%~pa
    for %%a in (%java_exe%) do set n=%%~na
    for %%a in (%java_exe%) do set x=%%~xa
    set n=%n:java=javaw%
    set javaw_exe=%p%%n%%x%
    if not exist %javaw_exe% set javaw_exe=%java_exe%
    goto :EOF

 

## 闪退的解决办法二：


先用管理员身份运行SDK Manager.exe，这时候会出现闪退，再在解压之后的SKD文件夹里搜索 android.bat ， 双击运行，这时候应该就会出现正常的程序窗口了