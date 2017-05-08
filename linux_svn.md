---
title: SVN配置
date: 2016-05-23 10:20:13
categories: linux
---
1，新建一个目录用于存储SVN所有文件 
mkdir /var/svn
2，新建一个版本仓库 
svnadmin create /var/svn/su
cd /var/svn/su/conf
3.
vim svnserve.conf
[general]
anon-access = none
auth-access = write
password-db = passwd
authz-db = authz
4.
vim passwd
[users]
su =123456
su1 = 654321
5.
vim authz
[groups]
web=su,su1



[/]
@web=rw


6.
客户端操作
svn://114.215.86.206/su
7.
vim post-commit

#!/bin/bash
WEB=/var/www/su
export LANG=en_US.UTF-8
svn update $WEB --username 'su1' --password '654321'
:wq 保存退出
使文件可以启动
chmod +x post-commit
8.
同步检出到网站web根目录
svn co svn://114.215.86.206/su /var/www/su
9.
杀掉SVN进程
killall  svnserve 
10.
svnserve -d -r /var/svn/su   启动成功。


//查看内容,,去掉#号
cat authz | grep -v ^#

