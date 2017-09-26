---
title: vpn
date: 2017-09-20 17:12:30
categories: others
tags:
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

linux 实现翻墙
```bash
yum install python-pip
在CentOS下需要用到pip，安装pip包，输入yum install python-pip，提示No package python-pip available.Error: Nothing to do
因为没有此rpm包，此包包含在epel源里面
输入rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm ，
之后再安装pip,输入yum install python-pip即可
pip install shadowsocks
vim /etc/shadowsocks.json
{
    "server":"服务器的ip",
    "server_port":19175,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"密码",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false
}

sslocal -c /etc/shadowsocks.json -d start
sslocal -c /etc/shadowsocks.json -d stop
sslocal -c /etc/shadowsocks.json -d restart

安装Privoxy
yum install -y privoxy
vim /etc/privoxy/config
增加
forward-socks5 / 127.0.0.1:1080 .

service privoxy start

执行vim /etc/profile,添加如下三句:

export http_proxy=http://127.0.0.1:8118
export https_proxy=http://127.0.0.1:8118
export ftp_proxy=http://127.0.0.1:8118

source /etc/profile

浏览器代理填写ip: 127.0.0.1 prot: 8118

```