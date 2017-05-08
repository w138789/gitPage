---
title: ngrok内网穿透
date: 2017-4-5 19:20:13
categories: others
---
一、 ngrok官网设置
1. 上 http://www.ngrok.cc  注册账号
2. 开通两条隧道tcp,http, 自定义域名解析记录类型为CHNAME, 记录值为sever.ngrok.cc
3. 下载客户端解压进入文件夹
4. ./sunny clientid 隧道id1,隧道id2

二、 ngrok其他服务器设置
1. http://ngrok.2bdata.com/ 上下载客户端
2. 解压客户端进入文件夹
3. 编辑ngrok.cfg
```bash
vim ngrok.cfg
server_addr: "tunnel.2bdata.com:4443"
trust_host_root_certs: false
tunnels:
  su:
    proto:
      http: "80"
  ssh:
    remote_port: 7026
    proto:
      tcp: "22"
:wq
```
4. 启动ngrok
```bash
./ngrok -config ngrok.cfg start su ssh
```