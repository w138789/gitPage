---
title: linux代理服务器(虚拟机)
date: 2016-05-23 10:20:13
categories: linux
---
三台机器分别为主机,squid-server,web-server

一. 普通代理服务器设置

1. 主机VMnet1和squid-server eth0 同一网段, squid-server eth1 和 web-server同一网段
主机 ip: 192.168.10.2
squid-server eth0 ip: 192.168.10.1 eth1 ip: 200.168.10.1
web-server eth0 ip: 200.168.10.2

2. squid-server安装squid应用程序
```bash
yum install -y squid
```

3. 配置squid-server配置文件
```bash
rm -rf /etc/squid/squid.conf
vim /etc/squid/squid.conf
http_port 192.168.10.1:3218 #代理服务器ip端口
visible_hostname 192.168.10.1 #代理服务器主机名称
acl innet src 192.168.10.0/24 #acl 名称列表
acl all src 0.0.0.0/0.0.0.0 #acl 所有名称列表
http_access allow innet #innet名称列表允许访问外网
http_access deny all #all名称禁止访问外网
:wq
service squid restart

3. web-server安装web服务程序

4. 主机浏览器设置代理服务器, 访问 200.168.10.2

透明代理暂时实现不了

二. 反向代理实现(让内网机器外网访问)

1. 主机VMnet1和squid-server eth0 同一网段, squid-server eth1 和 web-server同一网段
主机 ip: 200.168.10.2
squid-server eth0 ip: 200.168.10.1 eth1 ip: 192.168.10.1
web-server eth0 ip: 192.168.10.2

2. 配置squid-server配置文件
```bash
vim /etc/squid/squid.conf
http_port 200.168.10.1:80 vhost #开一虚拟ip端口
visible_hostname 192.168.10.1 #代理服务器主机名称
acl all src 0.0.0.0/0.0.0.0 #acl 所有名称列表
http_access allow all #all名称禁止访问外网
cache_peer 192.168.10.2 parent 80 0 originserver
:wq
squid -k reconfig

3. 主机浏览器设置代理服务器, 访问 200.168.10.1

三. 基于虚拟主机的squid.conf配置文件

1. vim /etc/squid/squid.conf
http_port 200.168.10.1:80 vhost #开一虚拟ip端口
visible_hostname 192.168.10.1 #代理服务器主机名称
acl all src 0.0.0.0/0.0.0.0 #acl 所有名称列表
http_access allow all #all名称禁止访问外网
cache_peer 192.168.10.2 parent 80 0 originserver name=a
cache_peer 192.168.10.2 parent 80 0 originserver name=b
cache_peer_domain a www.su1.com
cache_peer_domain b www.su2.com
cache_peer_access a allow all
cache_peer_access b allow all
:wq
squid -k reconfig


