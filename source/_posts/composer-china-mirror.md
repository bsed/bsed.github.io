---
title: Composer 中国镜像
date: 2013-10-15 12:55:00
updated: 2015-07-05 13:03:16
tags: 
- golang
categories: 
- go

---
# 1.用法：

有两种方式启用本镜像服务：

> 将以下配置信息添加到 Composer 的配置文件 config.json 中（系统全局配置）。见“例1”
> 将以下配置信息添加到你的项目的 composer.json 文件中（针对单个项目配置）。见“例2”


<!--more-->

为了避免安装包的时候都要执行两次查询，切记要添加禁用 packagist 的设置，如下：    

    {
            "repositories": [
                {"type": "composer", "url": "http://pkg.phpcomposer.com/repo/packagist/"},
                {"packagist": false}
            ]
        }
        

## 例1，修改 composer 的配置文件

查看 composer 主目录：通过 `composer config -l -g` 命令可以列出 composer 的全局配置信息，在这些信息中查找 [home] 配置项就是 composer 的主目录。Linux 用户请执行 `sudo composer config -l -g` 命令。

进入 composer 的主目录，打开 config.json 文件（如果没有就自己创建一个），然后增加本镜像服务的配置信息，最终内容如下：
  
      {
            "config": {
    
            },
            "repositories": [
                {"type": "composer", "url": "http://pkg.phpcomposer.com/repo/packagist/"},
                {"packagist": false}
            ]
        }
        

## 例2，以 laravel 项目的 composer.json 配置文件为例，添加本镜像服务配置信息后如下所示（注意最后几行）：

   

     {
            "name": "laravel/laravel",
            "description": "The Laravel Framework.",
            "keywords": ["framework", "laravel"],
            "license": "MIT",
            "type": "project",
            "require": {
                "laravel/framework": "4.2.*"
            },
            "autoload": {
                "classmap": [
                    "app/commands",
                    "app/controllers",
                    "app/models",
                    "app/database/migrations",
                    "app/database/seeds",
                    "app/tests/TestCase.php"
                ]
            },
            "scripts": {
                "post-install-cmd": [
                    "php artisan clear-compiled",
                    "php artisan optimize"
                ],
                "post-update-cmd": [
                    "php artisan clear-compiled",
                    "php artisan optimize"
                ],
                "post-create-project-cmd": [
                    "php artisan key:generate"
                ]
            },
            "config": {
                "preferred-install": "dist"
            },
            "minimum-stability": "stable",
            "repositories": [
                {"type": "composer", "url": "http://pkg.phpcomposer.com/repo/packagist/"},
                {"packagist": false}
            ]
        }
        
**说明：**

使用本镜像服务就意味着下载的所有的安装包元数据都将来自本镜像服务，而不再向 packagist.org 发起请求。这将加速 Composer 的安装过程，并且更加可靠、快速。（由于众所周知的原因，国外的网站连接速度太慢，并且随时可能被墙）

一般情况下，安装包的数据（zip 文件等）一般是从 GitHub 或其他第三方服务器上下载的，使用本镜像服务后，我们会缓存所有安装包到国内的机房，这样就不必再去向国外的网站发起请求，因此，即使 packagist.org、github.com 或其他第三方服务发生故障（主要是连接速度太慢和被墙），你仍然可以下载、更新安装包。

# 镜像2

    {
        "repositories": [
            {   
                "packagist": false
            },  
            {   
                "type": "composer", 
                "url": "http://packagist.cn"
            }   
        ]
    }

通过下面的命令，您可以观察速度提升的效果
但是Composer我也安装不了啊喂～

我们为composer installer也做了镜像，您可以尝试：

`$curl -sS http://packagist.cn/composer/installer | php`

如果没有安装curl，尝试以下命令（PHP你总得装吧喂～）

`$ php -r "readfile('http://packagist.cn/composer/installer');" | php`

当然，如果您不需要检查运行环境，也可以直接下载（一般来说直接下载就可以用）

[composer.phar](http://packagist.cn/composer/composer.phar)
[composer.exe](http://packagist.cn/composer/Composer-Setup.exe)

`composer update -vvv`