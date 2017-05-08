---
title: ftp双开
date: 2016-06-30 11:21:38
categories: linux
tags:
---
---
多用户FTP 对应不同文件夹.
#useradd -d /var/www/ftp1 -s /sbin/nologin ftpuserone
#passwd ftpuserone
#useradd -d /var/www/ftp2 -s /sbin/nologin ftpusertwo
#passwd ftpusertwo
修改ftp用户对应文件夹2
usermod -d /var/www/ftp3 ftpuserone

vsftpd出现553 Could not create file 解决方法，以Linux系统centos版本为例：

1、首先确保FTP目录文件是可写的，ls查看，如果不是修改权限。

```bash
ls -l
chmod 777 FTP目录
```
2、使用以下命令。

```bash
#查看ftpd_disable_trans ftp_home_dir allow_ftpd_full_access 是否为on。
getsebool -a|grep ftp
#如果不是则使用
setsebool allow_ftpd_full_access on #开这个就可以解决 
setsebool ftpd_disable_trans on
setsebool ftp_home_dir on
```
