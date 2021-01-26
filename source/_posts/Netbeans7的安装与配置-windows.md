---
title: "Netbeans7的安装与配置-windows"
categories: [ "Java" ]
tags: [ "windows","netbeans" ]
draft: false
slug: "netbeans7-install-for-windows"
date: "2014-11-20 22:15:00"
---

1. 推荐先安装依赖包，再安装Netbeans安装所需的依赖包

1.1由于Netbeans是使用JAVA编写的，首先安装JAVA虚拟机(必须)

http://java.com/download


<!--more-->


1.2 对于JavaSE开发，需要安装JDK(选装)

http://www.oracle.com/technetwork/java/javase/downloads/index.html

1.3 对于JavaEE开发，需要安装Java EE SDK(需要先安装JDK)(选装)

http://www.oracle.com/technetwork/java/javaee/downloads/index.html

注：如果JDK是64位版本，安装JavaEE可能需要手动指定JDK的位置

1.4 对于Python开发，需要安装Python解释器(选装)

http://www.python.org/getit/

1.5 对于ROR开发，需要安装Ruby+Rails

https://www.ruby-lang.org/zh_cn/downloads/

然后运行 gem install rails

由于国内访问rubygems非常慢，这里给大家推荐一个rubygems镜像，http://ruby.taobao.org/(淘宝的镜像)，访问飞快

也可以直接使用RailsInstaller

http://www.railsinstaller.org

1.6 对于c++开发，需要安装gcc,g++,make

首先下载cygwin

http://cygwin.com/install.html

运行安装文件。

接受缺省设置，直至转入 “Select Your Internet Connection” 页。在此页中选择最适合的选项。单击“下一步”。

在 “Choose A Download Site” 页中，选择一个方便下载的站点。单击“下一步”。

在 “Select Packages” 页中，选择要下载的软件包。单击 “Devel” 旁边的 “+” 号以展开此开发工具类别。可能需要调整窗口大小，以便能够一次查看更多内容。

单击要下载的每个软件包旁边的 “Skip” 标签以将其选中。至少需要选择 “gcc-core: C compiler”、”gcc-g++: C++ compiler”、”gdb: The GNU Debugger” 和 “make: The GNU version of the ‘make’ utility”。

现在将编译器目录添加到 Path 变量中：

打开“控制面板”（“开始”>“设置”>“控制面板”），然后双击“系统”程序。

选择“高级”标签，然后单击“环境变量”。

在“环境变量”对话框的“系统变量”面板中，选择 “Path” 变量，然后单击“编辑”。

将 cygwin-directory\bin 目录的路径添加到 Path 变量中，然后单击“确定”。缺省情况下，cygwin-directory 为 C:\cygwin。目录名称之间必须用分号进行分隔。

在“环境变量”和“系统属性”对话框中分别单击“确定”。

2.安装Netbeans

https://netbeans.org/downloads/

选择自己需要的版本，并进行安装。注意：必须先安装jre(见1.1)

2.1 配置Python

由于Netbeans7不再原生支持Python。我们需要手动安装插件。

在Netbeans中，依次打开 工具->插件->设置->添加

名称随便填，如Developer，URL填写：http://deadlock.netbeans.org/hudson/job/nbms-and-javadoc/lastStableBuild/artifact/nbbuild/nbms/updates.xml.gz

回到”可用插件”标签，搜索Python并安装。
        
2.2 配置ROR

由于Netbeans7不再原生支持ROR，oracle将它交给了开源社区，因此需要单独下载插件

http://plugins.netbeans.org/plugin/38549

下载之后解压，在update文件夹中有一个updates.xml文件，记下它的绝对路径。

在Netbeans中，依次打开 工具->插件->设置->添加

名称随便填，如Developer，URL填写解压出的updates.xml的绝对路径(注意转换成URL)，比如：file:/C:/updates/updates.xml

回到”可用插件”标签，选择Ruby on Rails并安装。

3.其他插件

3.1 vim插件

http://sourceforge.jp/projects/sfnet_viex/releases/

在Netbeans中，依次打开 工具->插件->已下载->添加插件，选择解压出来的start-module-myvim.nbm文件，并安装

安装完成后，在工具栏中会有一个VI图标，单击该图标启动vim编辑模式。