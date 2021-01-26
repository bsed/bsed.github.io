---
title: "管理Flask的配置文件"
categories: [ "Python" ]
tags: [ "python","flask","配置文件" ]
draft: false
slug: "664"
date: "2014-12-25 15:01:00"
---

所谓配置文件管理，就是在不改变源代码的前提下，拥有两套（甚至多套）配置文件，分别用于开发环境和生产环境，通过命令行选择配置文件。有些框架本身就支持多配置文件，例如Ruby On Rails，nodejs下的expressjs。python下的Flask虽然本身[支持配置文件管理](http://flask.pocoo.org/docs/config/)但单纯使用`from_object`和`from_envvar`却不是那么方便。有没有更好的办法？


<!--more-->


答案是[Flask-Environments](http://pythonhosted.org/Flask-Environments/)这个包。它能通过`FLASK_ENV`环境变量自动选择开发环境配置或生产环境配置。使用之前要先安装：

    $ sudo pip install Flask-Environments

然后修改`config.py`，将公用配置和默认配置写入`Config`类，针对环境的特殊配置写入各个环境的类：

    # config.py
    import os
    
    class Config(object):
      DEBUG = True
      BASEDIR = os.path.abspath(os.path.dirname(__file__))
    
      HOST = '0.0.0.0'
      PORT = '8000'
    
    class Development(Config):  # inherit from Config
      pass
    
    class Production(Config):
      DEBUG = False
      HOST = '127.0.0.1
      PORT = 14000

然后在生成`app`对象的地方加载配置：

    from flask import Flask
    from flask_environments import Environments
    
    app = Flask(__name__)
    env = Environments(app)
    env.from_object('config')

这样默认通过`app.run()`方式启动时会加载开发配置。若要启动生产环境，则要先设置环境变量`FLASK_ENV=PRODUCTION`：

    $ FLASK_ENV=PRODUCTION gunicorn -b 127.0.0.1:14000 myapp:app