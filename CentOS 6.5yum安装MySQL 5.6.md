---
title: CentOS 6.5yum安装MySQL 5.6
date: 2016-06-13 17:12:30
categories: mysql
tags:
---
背景
根据客户要求用系统自带的yum命令来安装MySQL 5.6，下面记录一下整个安装、配置过程。

CentOS 6.5 默认yum只能安装mysql 5.1
安装前要检查机器原来是否安装过mysql，如有安装需要先进行数据备份、清理。

[root@snails ~]# yum list installed | grep mysql
[root@snails ~]# ps -ef|grep mysql
[root@snails ~]# service mysqld stop 
[root@snails ~]# rpm -e mysql-libs --nodeps
[root@snails ~]# yum -y remove mysql mysql-*
设置安装源

[root@snails ~]# wget http://repo.mysql.com/mysql57-community-release-el6-8.noarch.rpm
[root@snails ~]# rpm -ivh mysql57-community-release-el6-8.noarch.rpm
[root@snails ~]# ls -1 /etc/yum.repos.d/mysql-community*
[root@snails ~]# yum repolist all | grep mysql
[root@snails ~]# vi /etc/yum.repos.d/mysql-community.repo
### 将[mysql56-community]的enabled设置为１,[mysql57-community]的enabled设置为0 ### 
[root@snails ~]# yum repolist enabled | grep mysql
mysql-connectors-community MySQL Connectors Community                         21
mysql-tools-community      MySQL Tools Community                              37
mysql56-community          MySQL 5.6 Community Server                        265
注意:
如果不使用5.7 或者其他任何版本，只能有一个是 enabled=1的，其他的都得enabled=0。
安装MySQL

[root@snails ~]# yum -y install mysql-server mysql

作者：技术学习
链接：http://www.jianshu.com/p/5a2e4e0b6eda
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。