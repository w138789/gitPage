---
title: redis配置
date: 2016-05-23 10:20:13
categories: linux
---
redis.conf
daemonize no 改为 daemonize yes 后台运行,,保证退出命令行不停止
appendonly no 改为 appendonly yes #数据持久化
dir ./ 改为 dir /usr/loca/redis/database #数据保存目录
port 6379 改为 port 6388 改变端口号


