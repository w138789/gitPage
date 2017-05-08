---
title: linux虚拟机LVS+DR负载均衡配置
date: 2017-03-05 10:20:13
categories: linux
---
开三台虚拟机
lvs: 开双网卡 ip1: 8.8.8.8 ip2: 192.168.10.1
real-server1: ip: 192.168.10.2
real-server2: ip: 192.168.10.3

一. LVS模式

1. 三台服务器都要关闭防火墙
```bash
service iptables stop
```
2. lvs 安装ipvsadm
```bash
yum install -y ipvsadm
```
3. real-server1和real-server2 安装nginx
```bash
yum install -y nginx
```

4. 本机设置VMnet1的ip地址: 8.8.8.1 子网掩码: 255.255.255.0

5. lvs 开双网卡, 网卡1设成VMnet1模式, 网卡2设成VMnet2模式
网卡1和网卡2各设置ip
```bash
ifconfig eth0 8.8.8.8 netmask 255.255.255.0
ifconfig eth1 192.168.10.1
```
6. real-server1设置eth0ip
```bash
ifconfig eth0 192.168.10.2
```
7. real-server2设置eth0ip
```bash
ifconfig eth0 192.168.10.3
```

8. 本机能ping通lvs 8.8.8.8, lvs 能ping通real-server1和real-server2

9. real-server1和real-server2增加默认网关
```bash
route add default gw 192.168.10.1
```

10. 开启路由模式
```bash
echo 1 >/proc/sys/net/ipv4/ip_forward
```

11. lvs 创建shell脚本
```bash
vim ipvs.sh
#!/bin/bash
#lvs.sh

ipvsadm -C
ipvsadm -At 8.8.8.8:80 -s rr
ipvsadm -at 8.8.8.8:80 -r 192.168.10.2:80 -m
ipvsadm -at 8.8.8.8:80 -r 192.168.10.3:80 -m
ipvsadm -L -n
:wq

chmod a+x ipvs.sh
./ipvs.sh
```

12. 浏览器输入8.8.8.8查看

二. DR模式负载均衡

1.四台机器要同一网络ip: 200.168.10.*

2. 同样要装ipvsadm + nginx

3. lvs建立ipvs.sh
```bash
vim ipvs.sh
#!/bin/bash
#lvs.sh

ifconfig eth0:0 200.168.10.10 netmask 255.255.255.255 up
route add -host 200.168.10.10 dev eth0:0
ipvsadm -C
ipvsadm -At 200.168.10.10:80 -s rr
ipvsadm -at 200.168.10.10:80 -r 200.168.10.2:80 -g
ipvsadm -at 200.168.10.10:80 -r 200.168.10.3:80 -g
ipvsadm -L -n
:wq
chmod a+x ipvs.sh
./ipvs.sh
```

4. real-server1+real-server2设置arp.sh
```bash
#!/bin/bash
#arp.sh

ifconfig lo:0 200.168.10.10 netmask 255.255.255.255 up
route add -host 200.168.10.10 dev lo:0
echo 1 >/proc/sys/net/ipv4/conf/lo/arp_ignore
echo 2 >/proc/sys/net/ipv4/conf/lo/arp_announce
echo 1 >/proc/sys/net/ipv4/conf/all/arp_ignore
echo 2 >/proc/sys/net/ipv4/conf/all/arp_announce
:wq
chmod a+x arp.sh
./arp.sh
```
5. 浏览器输入200.168.10.10查看