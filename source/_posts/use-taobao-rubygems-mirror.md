---
title: 使用淘宝 RubyGems 镜像
date: 2015-10-17 10:49:00
updated: 2015-11-01 13:57:25
tags: 
- java
- algorithms
categories: 
- algorithms

---
由于国内的网络环境，导致 rubygems.org 存放在 Amazon S3 上面的资源文件间歇性连接失败，因此使用gem或bundle时常常会遇到长久无响应的情况。

解决方案是使用淘宝的 [RubyGems](http://ruby.taobao.org/) 镜像，它是一个完整 rubygems.org 镜像，你可以用此代替官方版本，同步频率目前为15分钟一次以保证尽量与官方服务同步。**2014年以后， 镜像连接已经使用https 协议了。**

## 如何使用

### gem


<!--more-->


移除旧源，改用新源即可。

    $ gem sources --remove https://rubygems.org/
    $ gem sources -a https://ruby.taobao.org/
    $ gem sources -l
    *** CURRENT SOURCES ***
    
    https://ruby.taobao.org
    # 请确保只有 ruby.taobao.org
    $ gem install 你想安装的gem

### Bundle

    source 'https://ruby.taobao.org/'
    gem 'rails', '4.0.2'
    ...

## Ruby 源代码镜像

作为额外福利，淘宝还提供 Ruby 源代码下载镜像，改善国内 Ruby 安装的速度。

例如，使用 RVM，可以改用淘宝为下载源：

    $ sed -i 's!cache.ruby-lang.org/pub/ruby!ruby.taobao.org/mirrors/ruby!' $rvm_path/config/db

*其中$rvm_path 为自己的rvm 路径*
由于淘宝 http传输协议改成https ，需要进入$rvm_path/config/db 下的 `http://ruby.taobao.org/mirrors/ruby`
改为　`https://ruby.taobao.org/mirrors/ruby`
## 请确保只有 ruby.taobao.org

    $ gem install rails

如果你是用 Bundle (Rails 项目)

    source 'https://ruby.taobao.org/'
    gem 'rails', '4.1.0'
    ...

Ruby 源代码镜像
Ruby 源代码下载镜像

本镜像来源于 cache.ruby-lang.org 用于改善国内 Ruby 安装的速度。

    ruby-2.2.0.tar.gz
    ruby-2.1.4.tar.gz
    ruby-2.0.0-p451.tar.gz
    ruby-1.9.3-p545.tar.gz
    ruby-1.9.2-p320.tar.gz
    ruby-1.8.7-p358.tar.gz

修改 RVM ，改用本站作为下载源, 提高安装速度。
FOR MAC

    $ sed -i .bak 's!cache.ruby-lang.org/pub/ruby!ruby.taobao.org/mirrors/ruby!' $rvm_path/config/db

FOR LINUX

    $ sed -i 's!cache.ruby-lang.org/pub/ruby!ruby.taobao.org/mirrors/ruby!' $rvm_path/config/db

**常见问题**

    Q: 某些时候 gem install rails --pre （安装 preview 版本的 Gem）无法正确安装？

    A: 由于淘宝镜像这边没有实现 /api 下面的协议，而安装 pre 版本需要这些东西检查依赖，所以如果你需要这类安装需求的时候，请临时切换回官方的 RubyGems 源。

    Q: 为何我新发布的 Gem 在淘宝源上面无法安装？

    A: 由于同步是定期执行的，新发布的 Gem 可能没有那么快同步过来，你需要稍等一段时间后才能使用。

    Q: 已经换成淘宝源了，但 bundle install 或 gem install xxx 的时候卡住很久不动？

    A: 这有可能是你网络问题，或者没有正确的好 gem 的源，你可以尝试 gem install xxx -V 并把执行过程的结果在 Ruby China 上面发帖求助。

    Q: gem install xxx 的时候遇到错误信息包含：“Error fetching data: Errno::ETIMEDOUT: Operation timed out - connect(2)”

    A: 网络问题导致请求淘宝服务器被连接重置了，在遇到此类情况的时候，你可以尝试换一台机器或网络尝试安装，看是否还有同样的问题，以确定是淘宝镜像服务器的问题还是你的环境问题，如果你换了环境仍然有问题，请上 Ruby China 发帖求助。

