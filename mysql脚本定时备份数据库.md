---
title: mysql脚本定时备份数据库
date: 2016-06-13 17:12:30
categories: mysql
tags:
---
``` bash
创建文件夹
mkdir mysqlDump
创建脚本文件
vim mysqlDump.sh
today_date=`date +%Y%m%d`
today_time=`date +%T`


find /root/mysqlDump/ -mtime +10 -name "*.gz" -exec rm -rf {} \;


/usr/bin/mysqldump -u root -proot -A | gzip > /root/mysqlDump/db_${today_date}_${today_time}.sql.gz

:wq

#创建定时任务,,,12小时备份一次

crontab -e

0 */12 * * * /root/mysqlDump.sh

:wq

```