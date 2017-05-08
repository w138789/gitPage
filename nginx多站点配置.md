---
title: nginx多站点配置
date: 2016-06-13 13:40:42
categories: linux
tags:
---
当我们有了一个 VPS 主机以后，为了不浪费 VPS 的强大资源（相比共享主机1000多个站点挤在一台机器上），往往有想让 VPS 做点什么的想法，银子不能白花啊:)。放置多个网站或者博客是个不错的想法，可是如何配置 web 服务器才能在一个 VPS 上放置多个网站/博客呢？如何通过一个 IP 访问多个站点/域名呢？这就是大多数 web 服务器支持的 virtual hosting 功能。这里将描述如何一步一步如何用 nginx 配置 virtual hosting。

nginx 是一个小巧高效的 web 服务器，由俄罗斯程序员 Igor Sysoev 开发，nginx 虽然体积小，但功能一点也不弱，能和其他的 web 服务器一样支持 virtual hosting，即一个IP对应多个域名以支持多站点访问，就像一个IP对应一个站点一样，所以是”虚拟”的。你想在一个 IP 下面放多少个站点就放多少，只要硬盘够大就行。

这里以配置2个站点（2个域名）为例，n 个站点可以相应增加调整，假设：
``` bash
IP地址: 114.215.86.206
域名1 su1.sujianxun.com 放在 /var/www/su1
域名2 su2.sujianxun.com 放在 /var/www/su2
```
配置 nginx virtual hosting 的基本思路和步骤如下：
``` bash
把2个站点 su1.sujianxun.com su2.sujianxun.com 放到 nginx 可以访问的目录 /var/www/
给每个站点分别创建一个 nginx 配置文件 example1.com.conf，example2.com.conf, 并把配置文件放到 /etc/nginx/vhosts/
然后在 /etc/nginx.conf 里面加一句 include 把步骤2创建的配置文件全部包含进来（用 * 号）
重启 nginx
```

具体过程

下面是具体的配置过程：
1、在 /etc/nginx 下创建 vhosts 目录

``` bash
mkdir /etc/nginx/vhosts
```
2、在 /etc/nginx/vhosts/ 里创建一个名字为 su1.sujianxun.com.conf 的文件(名字无所谓)，把以下内容拷进去

``` bash
server {
    listen       80;
    server_name  su1.sujianxun.com;
    root /var/www/su1;
    #charset koi8-r;
    #access_log  /var/www/access.log  main;

    location / {
        index index.php index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
    #    root           html;
         fastcgi_pass   127.0.0.1:9000;
         fastcgi_index  index.php;
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

```

3、在 /etc/nginx/vhosts/ 里创建一个名字为 su2.sujianxun.com.conf 的文件(名字无所谓)，把以下内容拷进去

``` bash
server {
    listen       80;
    server_name  su2.sujianxun.com;
    root /var/www/su2/laravel/public;
    #charset koi8-r;
    #access_log  /var/www/access.log  main;

    location / {
        index index.php index.html index.htm;
        try_files $uri $uri/ /index.php$is_args$query_string;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
    #    root           html;
         fastcgi_pass   127.0.0.1:9000;
         fastcgi_index  index.php;
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

```

4、打开 /etc/nginix.conf 文件，在相应位置加入 include 把以上2个文件包含进来

``` bash
user  nginx;
worker_processes  1;

# main server error log
error_log	/var/log/nginx/error.log ;
pid	/var/run/nginx.pid;

events {
	worker_connections  1024;
}

# main server config
http {
	include       mime.types;
	default_type  application/octet-stream;
	log_format  main  '$remote_addr - $remote_user [$time_local] $request '
                      '"$status" $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

	sendfile        on;
	#tcp_nopush     on;
    	#keepalive_timeout  0;
	keepalive_timeout  65;
	gzip  on;

	server {
        	listen         80;
        	server_name     _;
        	access_log      /var/log/nginx/access.log main;
        	server_name_in_redirect  off;
        	location / {
            		root  /usr/share/nginx/html;
            		index index.html;
        	}
	}

    # 包含所有的虚拟主机的配置文件
    include /usr/local/etc/nginx/vhosts/*;
}
```

5、重启 Nginx

``` bash
/etc/init.d/nginx restart
```