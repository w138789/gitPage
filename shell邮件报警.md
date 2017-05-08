---
title: shell邮件报警
date: 2017-03-09 14:20:13
categories: linux
---

一. 编写报警脚本
```bash
在root文件夹下
#硬盘报警
vim disk.sh
#!/bin/bash
#disk.sh
#取得硬盘已使用空间
num=`df |awk 'NR==2{print int($5)}'`

if [ $num -gt 25 ]
then
	echo "disk is ${num}%, now > 25%"|mail -s 'disk' su1@su.com

fi

vim mysql.sh
#!/bin/bash
#mysql.sh
#监控3306端口是否开启
nc -w2 localhost 3306
if [ $? -ne 0 ]
then
	echo 'mysql 3306 port is down.'|mail -s 'mysql is down' su1@su.com
	service mysqld restart
fi

vim apache.sh
#!/bin/bash
#apache.sh
#监控80端口是否开启
nc -w2 localhost 80
if [ $? -ne 0 ]
then
	echo 'apache 80 port is down.'|mail -s 'apache is down' su1@su.com
	service httpd restart
fi
```
二. 做任务计划
```bash
[root@192 ~]# crontab -e

*/5 * * * * /root/disk.sh
*/5 * * * * /root/apache.sh
*/5 * * * * /root/mysql.sh
```

