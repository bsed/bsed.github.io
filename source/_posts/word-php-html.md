---
title: php word 转 html
date: 2015-10-13 20:47:00
updated: 2015-10-13 20:49:40
tags: 
- sql
- mysql
- cookbook
categories: 
- sql

---
要想完美解决，office转pdf或者html，最好还是用windows office软件，libreoffice不能完美转换，wps没有api。

先确认com模块是不是开启,phpinfo里面如果有com_dotnet模块，说明已开启，如果没有，修改php.ini,

    com.allow_dcom = true

前面的注释去掉，重启就OK了，php官方网站说，php5.4.5之前，com模块是内置的，其实也不一定全是，官网下的php 5.3.39，com模块就没有内置。
如果不是内置模块的话，php.ini加上，前提你的ext文件夹下，有该扩展

    extension=php_com_dotnet.dll

然后重启就OK了
 


<!--more-->


    function word2html($wordname,$htmlname)
     {
     $word = new COM("word.application") or die("Unable to instanciate Word");
     $word->Visible = 1;
     $word->Documents->Open($wordname);
     $word->Documents[1]->SaveAs($htmlname,8);
     $word->Quit();
     $word = null;
     unset($word);
     }
    
     word2html('D:/www/test/6.docx','D:/www/test/6.html');

*注意：*

 1. 转换出来的html，查看源码，比较乱的
 2. 转换过程中会调用winword.exe
 3. 如果页面一直在加载，把文档重命名，然后在重新转。

