---
title: php设置虚拟域名
date: 2016-05-27 11:20:14
categories: php
tags:
---
Apache配置
1.
``` bash
C:\phpStudy\Apache\conf\httpd.conf
//开启模块
LoadModule rewrite_module modules/mod_rewrite.so
Include conf/extra/httpd-vhosts.conf
```
2.
打开另一个文件
``` bash
C:\phpStudy\Apache\conf\extra\httpd-vhosts.conf
//设置代码
<VirtualHost 127.0.0.5:80>
    ServerAdmin webmaster@dummy-host.example.com
    #网站根目录
    DocumentRoot "d:/www/state"
    #域名
    ServerName su2.com
    ServerAlias www.dummy-host.example.com
    ErrorLog "logs/dummy-host.example.com-error.log"
    CustomLog "logs/dummy-host.example.com-access.log" common
    #开启伪静态
    RewriteEngine on 
    #如果文件夹有对应文件则打开对应文件
    RewriteCond %{DOCUMENT_ROOT}%{REQUEST_FILENAME} !-d
    RewriteCond %{DOCUMENT_ROOT}%{REQUEST_FILENAME} !-f
    #开启伪静态
    RewriteRule ^/([0-9]*).html$ /newlist.php?id=$1
</VirtualHost>
```
3.
打开另一个文件
``` bash
C:\Windows\System32\drivers\etc\hosts
//设置Apache对应域名
##########localhost################
127.0.0.1       su.com
127.0.0.5       su2.com
```
Nginx配置
1.
打开文件
``` bash
C:\phpStudy\nginx\conf\nginx.conf
location / {
	index  index.html index.htm index.php l.php;
	autoindex  on;
	if (!-e $request_filename){
		rewrite ^/([0-9]*).html$ /newlist.php?id=$1 last;
		break;
	}
}
打开另一个文件
C:\Windows\System32\drivers\etc\hosts
//设置Apache对应域名
##########localhost################
127.0.0.1       su.com
127.0.0.5       su2.com
```