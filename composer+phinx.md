---
title: composer+phinx
date: 2016-05-23 10:20:13
categories: php
---
1 安装composer
```bash
php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"
#全局安装
#linux
sudo mv composer.phar /usr/local/bin/composer
#win
#新建composer.bat
@php "%~dp0composer.phar" %*

#参考网站
https://pkg.phpcomposer.com/#how-to-install-composer

#全局安装
cp composer.phar /usr/local/sbin/composer

2 下载phinx
composer require robmorgan/phinx
#可能被墙
composer config -g repo.packagist composer https://packagist.phpcomposer.com
vendor/bin/phinx init
mkdir db/migrations
```
3 phinx 中文文档
https://tsy12321.gitbooks.io/phinx-doc/content/
```

安装错误:
1 PHP Warning:  copy(): Unable to find the wrapper "https" - did you forget to enable it when you configured PHP? in Command line code on line 1
安装openssl.so 扩展

