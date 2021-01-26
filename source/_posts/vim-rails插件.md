---
title: "vim-rails插件"
categories: [ "Vim" ]
tags: [ "vim","vim-rails" ]
draft: false
slug: "vim-plugins-vim-rails"
date: "2014-04-14 10:12:00"
---

这是最吸引我的一个插件，当你在写rails应用的时候可以提高效率。
一些常用功能：

gf功能：将光标停在对应的地方，然后按gf两个键就可以智能的跳到相应的文件，例如在user.rb中有这么一句has_many :comments, 然后将光标移到comments上，按gf就智能的跳到comment.rb文件；

:find :可以快速切换到对应的文件，如:find user.rb 就跳到user.rb文件，支持tab补全


<!--more-->


:Rmodel/:Rcontroller/:Rview :可以快速切换到对应的model controller view，如你在user.rb这个文件下，输入:Rcontroller命令就会切换到user的controller文件下。

......

一些命令

    :Rake      :Rake db:migrate,  :Rake db:create, ...... 
      :Rmodel     :Rmodel info (查找model名称为info的文件) 
      :Rview      :Rview  infos/new (查找infos控制器下的new视图文件) 
      :Rcontroller     :Rcontroller infos(查找控制器名称为infos的文件) 
      :find       :Rfind infos_controller(查找infos_controller.rb文件) 
      :Rails       :Rails console 或  :Rails generate model info age:integer或........ 
      :Rscript     :Rscript console 或 :Rscript generate model info age:integer或......(注意Rscript相当于script/rails命令) 
      :Redit       :Redit 相对路径 
      :Rlog        :Rlog development  打开development.log日志文件 
      :Rpreview     打开一个浏览器，http://localhost:3000 
      :Rrefresh     刷新 
      R             在目录下直接shift+r，可以刷新目录 
      gf            根据当前光标处内容跳转到文件 
      :Rmigration   查找migration文件 
      :Rlayout      查找layout文件 
      :Rhelper      查找helper文件 
      :Rstylesheet 
      :Rjavascript 
      :Rplugin 
      :Rlib 
      :Rtask 
      :Rserver

这里有两个非常棒的介绍：
[http://ruby-china.org/topics/4478](http://ruby-china.org/topics/4478)
[http://railscasts-china.com/episodes/rails-with-vim?autoplay=true](http://railscasts-china.com/episodes/rails-with-vim?autoplay=true)