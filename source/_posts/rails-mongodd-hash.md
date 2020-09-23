---
title: Rails中为Mongodb的Hash类型生成表单
date: 2014-08-27 22:37:00
updated: 2014-12-31 12:00:45
tags: 
- decode
categories: 
- java
- sql

---
把 Limlog 的数据库从 Mysql 迁移到了 Mongodb， 受够了各种 migrate 了。

在 app/model/reply.rb 中定义个回复者的相关信息

`field :author`

`field :email`

`field :url`


<!--more-->


但是 Ruby 讲究语意化，Reply 不可能有 email 和 url 这两个属性，于是看了 Mongoid 的 Document，发现了一个类型: Hash

于是将上述三列改为一列

`field :author, type: Hash, default: { name: '', email: '', url: '' }`
这样就明显符合人的思维～

改成这样之后， views 中的 form 也需要修改

比如上面的列，就需要使用 fields_for :author

`app/views/replies/_form.html.slim`

    = form_for [@post, @reply] do |f|
      = f.fields_for :author do |a|
        div
          = a.label      :name
          = a.text_field :name
        div
          = a.label      :email
          = a.text_field :email
        div
          = a.label      :url
          = a.text_field :url
      div
        = f.text_area    :content
        #tips
          span tips: using Ctrl + Enter to post your reply quickly
      div
        = f.submit       "Post Reply"

生成的表单的提交参数就是

    "reply" => {
      "author" => {
        "name" => "fasdfas",
        "email"=>"dfasdf",
        "url"=>"asdfasd"
      },
      "content"=>"fasdfas"
    }

另外, Strong paramsters 也需要修改

`app/controller/replies_controller.rb`

    def reply_params
      params.require(:reply).permit(:content, author: [:name, :email, :url])
        .merge(ip: request.env["HTTP_X_FORWARDED_FOR"],
               agent: request.env["HTTP_USER_AGENT"])
    end