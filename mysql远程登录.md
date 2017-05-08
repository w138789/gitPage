---
title: mysql远程登录
date: 2016-06-28 11:47:10
categories: mysql
tags:
---
mysql -u root -p
mysql>use mysql;
mysql>update user set host = '%' where user = 'root';
mysql>flush privileges;
mysql>select host, user from user;
可能要重启服务器才能生效