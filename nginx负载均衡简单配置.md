---
title: nginx负载均衡简单配置
date: 2017-03-03 14:20:13
categories: linux
---
nginx负载均衡简单配置

准备三台虚拟机来做这个实验：

192.168.232.132        web服务器
192.168.232.133        web服务器
192.168.232.134        负载均衡服务器

首先三台电脑预装nginx软件：

1、导入外部软件库
```bash
rpm -Uvh http://dl.iuscommunity.org/pub/ius/stable/Redhat/6/i386/epel-release-6-5.noarch.rpm  
rpm -Uvh http://dl.iuscommunity.org/pub/ius/stable/Redhat/6/i386/ius-release-1.0-10.ius.el6.noarch.rpm  
rpm -Uvh http://nginx.org/packages/centos/6/noarch/RPMS/nginx-release-centos-6-0.el6.ngx.noarch.rpm  
```

以下添加注释
```bash
mirrorlist=http://dmirr.iuscommunity.org/mirrorlist?repo=ius-el6&arch=$basearch  
```

以下删除注释
```bash
#baseurl=http://dl.iuscommunity.org/pub/ius/stable/Redhat/5/$basearch  
```

2、yum安装nginx
yum install nginx  
3、启动nginx
chkconfig nginx on  
service nginx start

向web服务器中放入测试文件：
```bash
<html>    
<head>    
<title>Welcome to nginx!</title>    
</head>    
<body bgcolor="white" text="black">    
<center><h1>Welcome to nginx! 192.168.232.132</h1></center>    
</body>    
</html>  
```


配置负载均衡服务器：
vi /etc/nginx/conf.d/default.conf
内容如下：
```bash
http {        #upstream模块包含在http模块下
    upstream  linuxidc{        #定义upstream名字，下面会引用
        server 192.168.1.100;        #指定后端服务器地址
        server 192.168.1.110;        #指定后端服务器地址
        server 192.168.1.120;        #指定后端服务器地址
    }

    server {
        listen 80;
        server name www.linuxidc.com;
        location / {
            proxy_pass http://linuxidc;        #引用upstream
        }
    }
}
```



下面浏览器打开:192.168.232.134，如果132、133交替显示则表明试验成功。

拓展：

1、轮询（默认）
每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

2、weight
指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。
例如：
```bash
upstream bakend {
server 192.168.159.10 weight=10;
server 192.168.159.11 weight=10;
}
```
3、ip_hash
每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。
例如：
```bash
upstream resinserver{
ip_hash;
server 192.168.159.10:8080;
server 192.168.159.11:8080;
}
```

4、fair（第三方）
按后端服务器的响应时间来分配请求，响应时间短的优先分配。
```bash
upstream resinserver{
server server1;
server server2;
fair;
}
```

5、url_hash（第三方）

按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。
例：在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法
```bash
upstream resinserver{
server squid1:3128;
server squid2:3128;
hash $request_uri;
hash_method crc32;
}
```
tips:
```bash
upstream resinserver{#定义负载均衡设备的Ip及设备状态
ip_hash;
server 127.0.0.1:8000 down;
server 127.0.0.1:8080 weight=2;
server 127.0.0.1:6801;
server 127.0.0.1:6802 backup;
}
```
在需要使用负载均衡的server中增加
```bash
proxy_pass http://resinserver/;
```

每个设备的状态设置为:
1.down 表示单前的server暂时不参与负载
2.weight 默认为1.weight越大，负载的权重就越大。
3.max_fails ：允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误
4.fail_timeout:max_fails次失败后，暂停的时间。
5.backup： 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。

nginx支持同时设置多组的负载均衡，用来给不用的server来使用。

client_body_in_file_only 设置为On 可以讲client post过来的数据记录到文件中用来做debug
client_body_temp_path 设置记录文件的目录 可以设置最多3层目录
location 对URL进行匹配.可以进行重定向或者进行新的代理 负载均衡