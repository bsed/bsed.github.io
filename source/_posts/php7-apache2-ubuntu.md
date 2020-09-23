---
title: PHP7 + Apache2 + Ubuntu
date: 2015-06-17 10:39:00
updated: 2017-03-18 11:01:13
tags: 
- css
- table
categories: 
- css

---
First add the new repository for PHP7
```
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:ondrej/php-7.0
```
Remove php5 and install php7


<!--more-->


backup any custom config
```
sudo apt-get update && sudo apt-get purge php5-fpm && sudo apt-get --purge autoremove && sudo apt-get install php7.0-fpm php7.0-mysql
```
Disable PHP5
```
sudo a2dismod php5

Create php 7 config from php5 file and enable PHP7
```
sudo cp /etc/apache2/mods-available/php5.conf /etc/apache2/mods-enabled/php7.conf
sudo a2enmod php7
sudo service apache2 restart
`
enjoy!
Bonus php7 curl
```
sudo apt-get install php7.0-curl
```