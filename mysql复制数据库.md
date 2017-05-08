---
title: mysql复制数据库
date: 2016-06-13 17:12:30
categories: mysql
tags:
---
复制一个数据库
说明: mysqldump 老数据库 -u root -p数据库密码 |mysql 新数据库 -u root -p数据库密码
``` bash
[root@localhost mysql]# mysqldump su -u root -p123456 |mysql su_new -u root -p123456
```