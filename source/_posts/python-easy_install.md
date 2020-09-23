---
title:  Python-- easy_install 的安装
date: 2014-12-20 22:39:00
updated: 2014-12-20 23:06:27
tags: 
- vundle
categories: 
- vim

---
下载安装python安装工具
下载地址：[http://pypi.python.org/pypi/setuptools](http://pypi.python.org/pypi/setuptools) 可以找到正确的版本进行下载。win7 32位可以下载setuptools-0.6c11.win32-py2.7.exe 。
Centos下的安装方法
`wget -q http://peak.telecommunity.com/dist/ez_setup.py`
python ez_setup.py


<!--more-->


注意：win7 64位必须使用ez_setup.py进行安装。方法是下载ez_setup.py后，在cmd下执行 python ez_setup.py，即可自动安装setuptools。目前没有直接的exe安装版本。

下载完成后双击执行安装文件，即可在D:\Program Files\python2.7\scripts下安装easy_install。包含一个easy_install.exe，然后进行环境变量设置，在系统环境变量中做如下设置：
(也就是将D:\Program Files\python2.7\scripts添加到环境变量中）
![2012071722244239.png][1]
此时可以在控制台看easy_install 是否安装上了。
 
Microsoft Windows [版本 6.1.7600]
版权所有 (c) 2009 Microsoft Corporation。保留所有权利。

C:\Users\zhuyupeng>easy_install
error: No urls, filenames, or requirements specified (see --help)
 
上面这种方式不正确，需要使用下面的方式：

C:\Users\zhuyupeng>easy_install virtualenv
Searching for virtualenv
Best match: virtualenv 1.7.2
Processing virtualenv-1.7.2-py2.7.egg
virtualenv 1.7.2 is already the active version in easy-install.pth
Installing virtualenv-script.py script to D:\Program Files\Python2.7\Scripts
Installing virtualenv.exe script to D:\Program Files\Python2.7\Scripts
Installing virtualenv.exe.manifest script to D:\Program Files\Python2.7\Scripts
Installing virtualenv-2.7-script.py script to D:\Program Files\Python2.7\Scripts

Installing virtualenv-2.7.exe script to D:\Program Files\Python2.7\Scripts
Installing virtualenv-2.7.exe.manifest script to D:\Program Files\Python2.7\Scri
pts

Using d:\program files\python2.7\lib\site-packages\virtualenv-1.7.2-py2.7.egg
Processing dependencies for virtualenv
Finished processing dependencies for virtualenv

安装lxml：
C:\Users\zhuyupeng>
 
 
如果直接使用：
easy_install lxml 
C:\Users\zhuyupeng> easy_install lxml 则会出现下面问题：
ERROR：‘xslt-config’ 不是内部或外部命令
make sure the developing packages of libxml2 and libxslt are installed windows
 
 
 
可以使用安装.egg 文件的方式来安装：
到
http://pypi.python.org/pypi/lxml/2.3/
网站上下载对应的lxml .egg 文件
然后使用下面的方式来安装：
C:\Users\zhuyupeng> easy_install D:\Program Files\Python2.7\lxml-2.3.py2.7.win32.egg
 
注：对于*.exe 文件这样也可以安装
 
通过这样的方式就可以安装成功了：
可以通过在控制台输入：easy_install lxml 来查看：
 
Microsoft Windows [版本 6.1.7600]
版权所有 (c) 2009 Microsoft Corporation。保留所有权利。

C:\Users\zhuyupeng>easy_install lxml
Searching for lxml
Best match: lxml 2.3.4
Adding lxml 2.3.4 to easy-install.pth file

Using d:\program files\python2.7\lib\site-packages
Processing dependencies for lxml
Finished processing dependencies for lxml

C:\Users\zhuyupeng>
 
 
安装了easy_install 之后安装python的库就很简单了，以后需要安装python的库的话则直接在命令行使用
easy_install + libname
 
比如：

C:\Users\zhuyupeng>easy_install numpy
Searching for numpy
...
Processing dependencies for numpy
Finished processing dependencies for numpy


这是下载下来再安装的：
C:\Users\zhuyupeng>easy_install "D:\Program Files\Python2.7\matplotlib-1.1.0.win
32-py2.7.exe"
...
Processing dependencies for matplotlib==1.1.0
Finished processing dependencies for matplotlib==1.1.0

C:\Users\zhuyupeng>


  [1]: https://imgs.gnux.cn/usr/uploads/2014/12/927817459.png