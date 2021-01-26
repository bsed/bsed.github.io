---
title: "rails console 报 ruby 解释器错误"
categories: [ "Rails" ]
tags: [ "rails" ]
draft: false
slug: "rails-console-report-ruby-interpreter-error"
date: "2017-10-22 12:03:00"
---

最近碰到一个奇怪的问题

在运行完rails生成器生成模型／控制器等文件后，再进入沙盒环境的rails conosle就会报ruby解释器错误：

`rails g model User name:stringrails console --sandbox#抛出错误#上方行数太多了,terminal无法显示完全.... 1558 /Users/x/.rvm/gems/ruby-2.3.1/gems/railties-5.0.0.1/lib/rails/commands/console_helper.rb 1559 /Users/x/.rvm/gems/ruby-2.3.1/gems/railties-5.0.0.1/lib/rails/commands/console.rb 1560 /Users/x/.rvm/gems/ruby-2.3.1/gems/railties-5.0.0.1/lib/rails/console/app.rb 1561 /Users/x/.rvm/gems/ruby-2.3.1/gems/railties-5.0.0.1/lib/rails/console/helpers.rb[NOTE]You may have encountered a bug in the Ruby interpreter or extension libraries.Bug reports are welcome.For details: http://www.ruby-lang.org/bugreport.html`

如果运行完数据库迁移，在rails console里创建一个模型实例也是抛出一样的错误。


<!--more-->


`rails g model User name:stringrails db:migraterails consoleRunning via Spring preloader in process 56859Loading development environment (Rails 5.0.0.1)2.3.1 :001 > User.new#抛出错误... 1574 /Users/x/.rvm/gems/ruby-2.3.1/gems/activerecord-5.0.0.1/lib/active_record/relation/batches.rb 1575 /Users/x/.rvm/gems/ruby-2.3.1/gems/activerecord-5.0.0.1/lib/active_record/relation.rb 1576 /Users/x/.rvm/gems/ruby-2.3.1/gems/activerecord-5.0.0.1/lib/active_record/associations/collection_proxy.rb 1577 /Users/x/.rvm/gems/ruby-2.3.1/gems/activerecord-5.0.0.1/lib/active_record/association_relation.rb[NOTE]You may have encountered a bug in the Ruby interpreter or extension libraries.Bug reports are welcome.For details: http://www.ruby-lang.org/bugreport.html`

不确定是不是升级到macOS10.12造成的？

解决：

一般都是 Ruby 当前编译的版本与操作系统版本不同造成的. 这个问题很可能跟 macOS 10.12 有关.

运行完生成器或者迁移都需要spring stop一下才可以， 我把spring的gem给移除掉了后就没这个问题了。

还有时候会出现语法错误：

首先我们要看error提示，比如说/Users/xiaoshouxiaoshou/job2/job-listing/config/routes.rb:7: syntax error, unexpected end-of-input, expecting keyword_end (SyntaxError)

synta 句法
error 错误
说明语法错误

unexpected 意外
end-of-input 输入的意外终止，也就是页面代码写的不规范，其中的某条语句，没有正常结束…
expecting keyword_end 期望（缺少）end关键字

出错位置在：/Users/xiaoshouxiaoshou/job2/job-listing/config/routes.rb

打开编辑器看到
Rails.application.routes.draw do
devise_for :users
namespace :admin do
resources :jobs
root "jobs#index"
\# For details on the DSL available within this file, see <http://guides.rubyonrails.org/routing.html>
end

正确应该再加上一个end
Rails.application.routes.draw do
devise_for :users
namespace :admin do
resources :job
end （以上是管理员权限，以下属于一般登录者，所以要用end断开
root "jobs#index"
\# For details on the DSL available within this file, see <http://guides.rubyonrails.org/routing.html>
end

原文：http://yuanxianyaquanzhan-blog.logdown.com/posts/1436907-enter-the-rails-console-ruby-interpreter-errors