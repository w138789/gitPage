---
title: linux命令
date: 2016-06-17 15:35:16
categories: linux
tags:
---
1.删除文件夹下所有文件
进入这个文件夹
然后用命令 rm -rf *
删除单个文件
rm -rf su1.conf
2.重启服务器启动网站服务
//重启服务器重启svn
svnserve -d -r /var/svn
//启动php-fpm
service php-fpm start
service httpd start
service mysqld start
/etc/init.d/vsftpd start
3.修改文件夹可写权限
chmod -R a+w 目录名   -R是递归久是在该目录下所有文件将都按照这个权限设置
4.查找文件位置
find / -name access.log //文件名字
5.复制文件
cp su1.conf su2.conf (同一文件夹下) 或者 cp su1.conf /etc/nginx/vhost/su2.conf (复制到另一个文件夹下)
6.重命名文件
mv su3.conf su1.conf
7.压缩忽略单个文件夹
``` bash
zip -r lanren11.zip * -x "images*"
```
压缩忽略多个文件夹
``` bash
zip -r lanren22.zip * -x "data*" -x "ueditor*"
```
每秒跟踪文件存储
```bash
watch -n1 ls -lh
#查看cpu使用
top
#查看内存使用
free -m
```
加入开机自启动
```bash
#查看开机自启动
[root@iZbp14a08o2w4yosoq3h1mZ ~]# chkconfig --list nginx
nginx          	0:off	1:off	2:on	3:on	4:on	5:on	6:off
#启动开机自启
[root@iZbp14a08o2w4yosoq3h1mZ ~]# chkconfig nginx on
#关闭开启自启
[root@iZbp14a08o2w4yosoq3h1mZ ~]# chkconfig nginx off
```