---
title: vpn翻墙
date: 2016-05-23 10:20:13
categories: vpn
---
1购买bandwagonhost服务器
搬瓦工，因其官网网站标识是BandwagonHost，有点类似BanWaGong的拼写，所以我们国内的站长喜欢称作为搬瓦工VPS。

https://bandwagonhost.com/index.php
登录网站后，点击导航栏中【VPS Hosting】按钮购买


修改VPS密码
使用ssh工具登录服务器，输入VPS的IP地址、端口号、用户名和密码。具体登录方法不在本次讲解的范围内，恕不赘述。
然后输入shell命令
```bash
passwd
```

安装shadowsocks
```bash
wget –no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks.sh  
chmod +x shadowsocks.sh  
./shadowsocks.sh 2>&1 | tee shadowsocks.log  
```

配置开机自动启动
```bash
打开rc.local文件

vi /etc/rc.local

输入下面的代码

ssserver -c /etc/shadowsocks.json -d start

shadowsocks服务相关命令

启动shadowsocks服务

ssserver -c /etc/shadowsocks.json -d start

关闭shadowsocks服务

ssserver -c /etc/shadowsocks.json -d stop

重启shadowsocks服务

ssserver -c /etc/shadowsocks.json -d restart
```
教程地址: 
http://blog.csdn.net/win_turn/article/details/51559867
http://blog.csdn.net/qq_19835843/article/details/52233111