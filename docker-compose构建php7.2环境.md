---
title: docker命令
date: 2020-09-23 10:20:13
categories: docker
---
```bash
1. 新建docker-compose.yml构建文件
vim docker-compose.yml
version: "2.1"
services:
    nginx:
        image: nginx
        ports:
            - "80:80"
        volumes: 
            - ${BASE_HOST}:/usr/share/nginx/html
            - ./nginx/conf:/etc/nginx/conf.d
            - ./nginx/logs:/var/log/nginx
        networks:
            - lnmp-network
    php:
        build: 
            context: ./dockerFile/php
            dockerfile: Dockerfile
        volumes:
            - ${BASE_HOST}:/www
        networks:
            - lnmp-network
    mysql:
        build: 
            context: ./dockerFile/mysql
            dockerfile: Dockerfile
        volumes:
            - ${MYSQL_DATA}:/var/lib/mysql
        ports:
            - "3306:3306"
        environment:
            - MYSQL_ROOT_PASSWORD=123456
            - TZ=Asia/Shanghai
        networks:
            - lnmp-network
    redis:
        image: redis
        ports:
            - "6379:6379"
        networks:
            - lnmp-network
networks: 
    lnmp-network:
:wq

2. 设置docker-compose.yml构建文件变量
vim .env
# 代码目录
BASE_HOST=/home/su/www
# 放置mysql数据库文件目录
MYSQL_DATA=/home/su/software/mysql5.7.31/data
:wq

3. 新建nginx配置文件
mkdir -p nginx/conf
vim nginx/conf/shop.conf
server {
    listen       80;
    server_name  www.shop.com;
    root /usr/share/nginx/html/shop/public;

    #deny 127.0.0.1;

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
         fastcgi_param  SCRIPT_FILENAME /www/shop/public/$fastcgi_script_name;
         include        fastcgi_params;
     }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
:wq

4. 新建php Dockerfile
mkdir -p dockerFile/php
vim dockerFile/php/Dockerfile
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
&& docker-php-ext-install pdo pdo_mysql \
&& curl -L -o /tmp/redis.tar.gz https://github.com/phpredis/phpredis/archive/3.1.3.tar.gz \
&& tar xfz /tmp/redis.tar.gz \
&& rm -r /tmp/redis.tar.gz \
&& mkdir -p /usr/src/php/ext \
&& mv phpredis-3.1.3 /usr/src/php/ext/redis \
&& docker-php-ext-install redis \
&& docker-php-ext-install bcmath \
:wq

5. 新建mysql Dockerfile
mkdir -p dockerFile/mysql
vim dockerFile/mysql/Dockerfile
FROM mysql:5.7.31
RUN echo '#修改编码\ncharacter-set-server=utf8'>>/etc/mysql/mysql.conf.d/mysqld.cnf
:wq

6. 开始构建php7.2
docker-compose up --build
```