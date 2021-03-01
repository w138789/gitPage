---
title: docker命令
date: 2020-09-23 10:20:13
categories: docker
---
```bash
1. mysql安装

#拉取镜像
docker pull mysql:latest

#运行容器
docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql

#绑定主机的数据库存放目录: /home/su/test/mysqlData
docker run -itd --name mysql  -p 3306:3306 -v /home/su/test/mysqlData:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql

mysql服务远程连接

#进入容器
docker exec -it mysql bash

#登录mysql
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';

#直接设置root用户为远程登录用户
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';

#添加远程登录用户
CREATE USER 'sujia'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
GRANT ALL PRIVILEGES ON *.* TO 'sujia'@'%';
FLUSH PRIVILEGES;


2. php安装

#空文件夹建立文件: Dockerfile
#加入内容:

FROM php:7.2-fpm-stretch
 
RUN echo "deb http://mirrors.aliyun.com/debian/ stretch main non-free contrib" > /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/debian/ stretch main non-free contrib" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/debian-security stretch/updates main" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/debian-security stretch/updates main" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/debian/ stretch-updates main non-free contrib" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/debian/ stretch-updates main non-free contrib" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/debian/ stretch-backports main non-free contrib" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/debian/ stretch-backports main non-free contrib" >> /etc/apt/sources.list \
    && rm -f /etc/apt/sources.list.d/* \
    && apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install pdo_mysql && docker-php-ext-install mysqli
EXPOSE 9000
CMD ["php-fpm"]

#拉取及构建镜像
docker build -t php .

#运行容器
docker run --name  php -v /home/su/www:/www  -d php

#创建 ~/nginx/conf/conf.d 目录：

mkdir ~/nginx/conf/conf.d 

#在目录下新建文件: default.conf

server {
    listen       80;
    server_name  www.test.com;
    root /www/test;
    #charset koi8-r;
    #access_log  /var/www/access.log  main;
    location / {
        index index.php index.html index.htm;
        #try_files $uri $uri/ /index.php$is_args$query_string;
        if (!-e $request_filename) {
                #一级目录
                rewrite ^/(.*)$ /index.php/$1 last;
                #二级目录
                rewrite ^/MYAPP/(.*)$ /MYAPP/index.php/$1 last;
        }
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    #error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/local/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #


location ~ \.php($|/) {
    #    root           html;
         fastcgi_pass   php:9000;
         fastcgi_index  index.php;
         fastcgi_split_path_info ^(.+\.php)(.*)$;
         fastcgi_param   PATH_INFO $fastcgi_path_info;
         fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
         include        fastcgi_params;
     }


    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

#/www/test/: 是  php 文件的存储路径，映射到本地的 ~/nginx/www 目录, php程序文件放在这个目录。
#拉取镜像
docker pull nginx
#启动nginx
docker run --name nginx -p 80:80 -d \
    -v ~/nginx/www:/usr/share/nginx/html:ro \
    -v ~/nginx/conf/conf.d:/etc/nginx/conf.d:ro \
    --link php:php \
    nginx

docker通过双容器和nginx进行平滑重启
1. 建立两个容器,分别设置容器端口不一样.
2. 主机建立nginx配置文件, 通过注释server, service nginx reload 来进行平滑重启
vim test.conf
# 至少需要一个 Hyperf 节点，多个配置多行
upstream hyperf {
    # Hyperf HTTP Server 的 IP 及 端口
    server 127.0.0.1:9501;
    #server 127.0.0.1:9502;
}

server {
    # 监听端口
    listen 80; 
    # 绑定的域名，填写您的域名
    server_name www.hyperf.com;

    location / {
        # 将客户端的 Host 和 IP 信息一并转发到对应节点  
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        # 转发Cookie，设置 SameSite
        proxy_cookie_path / "/; secure; HttpOnly; SameSite=strict";

        # 执行代理访问真实服务器
        proxy_pass http://hyperf;
    }
}

# 删除镜像
docker rmi -f $(docker images -qa)
# 删除容器
docker rm $(docker ps -aq)

#容器加入现有网络
docker network connect base_net web
:wq
```