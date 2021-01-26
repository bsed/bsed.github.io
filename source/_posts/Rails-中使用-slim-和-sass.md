---
title: "Rails 中使用 slim 和 sass"
categories: [ "Rails" ]
tags: [ "sass","rails","slim" ]
draft: false
slug: "rails-use-slim-sass"
date: "2014-08-27 22:17:00"
---

Rails 中默认是使用 erb 和 scss，写了一段时间 erb 和 scss，越来越觉得这两个 DSL 不简洁，而 slim 和 sass 则比较简洁，于是就决定迁移到 slim 和 sass

Rails 中默认使用 slim 和 sass

在 config/application.rb 里 class Application < Rails::Application 中加入以下片段
<!--more-->
    config.sass.preferred_syntax= :sass
    
    config.generators do |g|
      g.template_engine :slim
    end

这样以后使用 rails g controller 时生成的就是 .slim 和 .sass 了

对于现有的 erb 和 scss，可以分别使用 erb2slim 和 sass-convert 进行迁移