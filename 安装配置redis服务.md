---
title: 安装配置redis服务
date: 2019-01-15 10:20:13
categories: linux
---
复制文件到文件夹下
sudo cp -r /home/su/php/redis-4.0.9 /usr/local/redis4.0.9
复制conf文件到文件夹下
sudo cp /home/su/php/redis-4.0.9/安装方法/redis.conf /usr/local/redis4.0.9/
复制服务启动文件到服务文件夹下
sudo cp /home/su/php/redis-4.0.9/安装方法/redis /etc/init.d/redis
加入服务
chkconfig --add redis

redis.conf
指定本地数据库存放目录
dir ./  改为 /usr/local/redis4.0.9/db
指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
appendonly no 改为 appendonly yes
Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
daemonize no 改为 daemonize yes
