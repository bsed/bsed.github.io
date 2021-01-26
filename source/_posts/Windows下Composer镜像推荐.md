---
title: "Windows下Composer镜像推荐"
categories: [ "PHP" ]
tags: [ "php","composer" ]
draft: false
slug: "recommendation-of-composer-image-under-windows"
date: "2020-04-08 18:29:00"
---

自从 Packagist 中国全量镜像服务停止以后，分享一些国内可用的 Composer 全量镜像.

## Alibaba Cloud Composer Mirror
由阿里提供的镜像，阿里家大业大，大水管

镜像地址：[https://mirrors.aliyun.com/composer/](https://mirrors.aliyun.com/composer/)

## 腾讯云
镜像地址：[https://mirrors.cloud.tencent.com/composer/](https://mirrors.cloud.tencent.com/composer/)


<!--more-->


## 安畅网络赞助的镜像

镜像地址：[https://php.cnpkg.org](https://php.cnpkg.org)

## 上海交通大学提供的镜像

镜像地址：[https://packagist.mirrors.sjtug.sjtu.edu.cn](https://packagist.mirrors.sjtug.sjtu.edu.cn)

Packagist / Composer 中国全量镜像

镜像地址：https://packagist.phpcomposer.com

## 如何修改镜像源
默认源地址在国外，从国内访问的速度肯定比较慢，所以我们需要修改镜像源，以下以阿里提供的镜像为例，替换对应镜像地址即可

配置只在当前项目生效
```bash
composer config repo.packagist composer https://mirrors.aliyun.com/composer/
composer config --unset repos.packagist # 取消当前项目配置
```
配置全局生效
```bash
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
composer config -g --unset repos.packagist # 取消全局配置
```
使用第三方软件快速修改、切换镜像源

CRM – Composer 源管理工具：[https://github.com/slince/composer-registry-manager](https://github.com/slince/composer-registry-manager)

刷新 composer.lock 文件
使用以下命令：
```bash
composer update nothing
```
或者：
```bash
composer update --lock
```
–lock: 只升级 lock 文件的哈希以消除 lock 文件过期的警告

清空本地缓存：
```bash
rm -rf vendor
composer clear-cache
```
执行安装命令：
```bash
composer install -vvv
```