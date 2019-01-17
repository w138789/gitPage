---
title: supervisor进程管理
date: 2019-01-16 15:35:16
categories: linux
tags:
---
安装
yum install -y supervisor

启动服务
sudo service supervisor start
查看进程
sudo supervisorctl status
```bash
su@su:/var/www/santai$ sudo supervisorctl status
jPush                            RUNNING   pid 8910, uptime 0:20:00
wechatMessage                    RUNNING   pid 8909, uptime 0:20:00
```
杀死服务
sudo pkill supervisord

配置文件:
sudo vim /etc/supervisor/conf.d/jPush.conf
添加:
```bash
[program:jPush]
command= /usr/local/php7.2.5/bin/php think queue:work --queue="jPush" --delay=0 --memory=128 --sleep=3 --tries=0 --daemon  
directory= /var/www/santai 
user=su  ;启动进程用户
;process_name=%(process_num)02d
;numprocs=5 ;启动几个进程 
autostart=true ;随着supervisord的启动而启动 
autorestart=true ;自动启动 
startsecs=1 ;程序重启时候停留在runing状态的秒数 
startretries=5 ;启动失败时的最多重试次数 
redirect_stderr=true ;重定向stderr到stdout  
stdout_logfile=/var/www/santai/log/supervisor/jPush ;stdout文件
```
:wq
多个进程复制多个文件
服务开启的时候可能其中一个无法正常启动,需要手动启动相应进程
sudo supervisorctl start jPush