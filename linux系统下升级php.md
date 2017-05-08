---
title: linux系统下升级php
date: 2016-05-30 17:50:42
categories: linux
tags:
---
首先安装一下python-software-properties

``` bash
sudo apt-get update && sudo apt-get install python-software-properties
```
添加php5.5的ppa

``` bash
sudo add-apt-repository ppa:ondrej/php5
```
如果你想保守一点，只升级到php5.4稳定版那么可以添加下面这个ppa

``` bash
sudo add-apt-repository ppa:ondrej/php5-oldstable
//或者使用下面这个升级到php5.6
sudo add-apt-repository ppa:ondrej/php5-5.6
```
如果执行上面的代码提示“add-apt-repository not found”，可以执行下面的代码进行修复


sudo apt-get install software-properties-common python-software-properties

最后执行一下更新和升级，这一步可能会比较花费时间

``` bash
sudo apt-get update && sudo apt-get dist-upgrade
```
升级完成之后

1.如果你的web根目录不是/var/www那么你需要在/etc/apache2/apache2.conf重新进行配置；

2.如果你安装了php5-xdebug那么在升级之后php无法找到这个模块，你只要彻底删除这个模块然后重新安装即可

``` bash
sudo apt-get remove --purge php5-xdebug

sudo apt-get install php5-xdebug
```

CentOS yum 安装 PHP 5.6.24
配置yum源

追加CentOS 6.5的epel及remi源。

# rpm -Uvh http://ftp.iij.ad.jp/pub/linux/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
# rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm

使用yum list命令查看可安装的包(Packege)。

# yum list --enablerepo=remi --enablerepo=remi-php56 | grep php
安装PHP5.6

yum源配置好了，下一步就安装PHP5.6。

# yum install --enablerepo=remi --enablerepo=remi-php56 php php-opcache php-devel php-mbstring php-mcrypt php-mysqlnd php-phpunit-PHPUnit php-pecl-xdebug php-pecl-xhprof
用PHP命令查看版本。

# php --version
PHP 5.6.0 (cli) (built: Sep 3 2014 19:51:31)
Copyright (c) 1997-2014 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2014 Zend Technologies
with Zend OPcache v7.0.4-dev, Copyright (c) 1999-2014, by Zend Technologies
with Xdebug v2.2.5, Copyright (c) 2002-2014, by Derick Rethans

//安装php-fpm
yum install --enablerepo=remi --enablerepo=remi-php56 php-fpm

Centos下Yum安装PHP5.5,5.6,7.0
默认的版本太低了，手动安装有一些麻烦，想采用Yum安装的可以使用下面的方案：
1.检查当前安装的PHP包
yum list installed | grep php
如果有安装的PHP包，先删除他们
 yum remove php.x86_64 php-cli.x86_64 php-common.x86_64 php-gd.x86_64 php-ldap.x86_64 php-mbstring.x86_64 php-mcrypt.x86_64 php-mysql.x86_64 php-pdo.x86_64
2.Centos 5.X
  rpm -Uvh http://mirror.webtatic.com/yum/el5/latest.rpm
  CentOs 6.x
  rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm
  CentOs 7.X
rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
如果想删除上面安装的包，重新安装
rpm -qa | grep webstatic
rpm -e  上面搜索到的包即可

3.运行yum install
yum install php55w.x86_64 php55w-cli.x86_64 php55w-common.x86_64 php55w-gd.x86_64 php55w-ldap.x86_64 php55w-mbstring.x86_64 php55w-mcrypt.x86_64 php55w-mysql.x86_64 php55w-pdo.x86_64

yum install php56w.x86_64 php56w-cli.x86_64 php56w-common.x86_64 php56w-gd.x86_64 php56w-ldap.x86_64 php56w-mbstring.x86_64 php56w-mcrypt.x86_64 php56w-mysql.x86_64 php56w-pdo.x86_64

注：如果想升级到5.6把上面的55w换成56w就可以了。

yum install php70w.x86_64 php70w-cli.x86_64 php70w-common.x86_64 php70w-gd.x86_64 php70w-ldap.x86_64 php70w-mbstring.x86_64 php70w-mcrypt.x86_64 php70w-mysql.x86_64 php70w-pdo.x86_64
4.安装PHP FPM
yum install php55w-fpm 
yum install php56w-fpm 
yum install php70w-fpm
注：如果想升级到5.6把上面的55w换成56w就可以了。
//如php文件不解析
vim etc/nginx/nginx.conf
增加
location ~ \.php$ {
            root          /home/www/wwwroot;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
           fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }