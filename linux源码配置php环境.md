---
title: linux源码配置php环境
date: 2017-03-07 10:20:13
categories: linux
---
一、安装Apache2.2.22
```bash
tar  -zxvf httpd-2.2.22.tar.gz
cd /httpd-2.2.22
./configure --prefix=/usr/local/apache2 --enable-module=shared
make
make install
cd /usr/local/apache2/bin
./apachectl -k start //启动
./apachectl -k restart //重启
./apachectl -k stop //停止
```
配置文件(满足最基本的配置)
编辑 /usr/local/apache2/conf/httpd.conf 文件     
找到：
AddType  application/x-compress .Z
AddType application/x-gzip .gz .tgz
在后面添加：
AddType application/x-httpd-php .php（使Apcche支持PHP）
AddType application/x-httpd-php-source .php5   
找到：
<IfModule dir_module>
DirectoryIndex index.html
</IfModule>
添加：
<IfModule dir_module>
DirectoryIndex index.html index.php
</IfModule>    
找到：
＃ServerName www.example.com:80
修改为：
ServerName 127.0.0.1:80或者ServerName localhost:80
记得要去掉前面的“＃”    

二.安装mysql
1、下载地址 
wget http://downloads.mysql.com/archives/get/file/mysql-5.6.27-linux-glibc2.5-x86_64.tar.gz
```bash
#解压
tar -zxvf mysql-5.6.33-linux-glibc2.5-x86_64.tar.gz
#复制解压后的mysql目录
cp -r mysql-5.6.33-linux-glibc2.5-x86_64 /usr/local/mysql
#添加用户组
groupadd mysql
#添加用户mysql 到用户组mysql
useradd -g mysql mysql
#安装
cd /usr/local/mysql/<br>mkdir ./data/mysql
chown -R mysql:mysql ./
./scripts/mysql_install_db --user=mysql --datadir=/usr/local/mysql/data/mysql
cp support-files/mysql.server /etc/init.d/mysqld
chmod 755 /etc/init.d/mysqld
cp support-files/my-default.cnf /etc/my.cnf
#修改配置文件
vim /etc/my.cnf
#添加
#修改编码
character-set-server=utf8
#修改默认存储引擎
default-storage-engine=MyISAM 
:wq
#修改启动脚本
vi /etc/init.d/mysqld
#修改项：
basedir=/usr/local/mysql/
datadir=/usr/local/mysql/data/mysql
#启动服务
service mysqld start
#测试连接
./mysql/bin/mysql -uroot
#查看mysql编码及程序信息
\s
#启动mysql
service mysqld start
#关闭mysql
service mysqld stop
#查看运行状态
service mysqld status
6.1 配置环境变量
vi + /etc/profile
export PATH=....:/usr/local/mysql/bin
```

三、安装PHP 
1. 解压
```bash
tar -zxvf php-5.3.16.tar.gz
cd /php-5.3.16
./configure --prefix=/usr/local/php \
--with-apxs2=/usr/local/apache2/bin/apxs \
--with-mysqli=/usr/local/mysql/bin/mysql_config \
--with-pdo-mysql=/usr/local/mysql/ \
--with-mysql \
 --enable-fpm \
--with-php-config=/usr/local/php/bin/php-config  注意mysql_config 的路径

#下面是nginx的
./configure --prefix=/usr/local/php \
--with-mysqli=/usr/local/mysql/bin/mysql_config \
--with-pdo-mysql=/usr/local/mysql/ \
--with-mysql \
--enable-fpm \
--with-php-config=/usr/local/php/bin/php-config 

```
注意这里有一个-with-apxs2=/usr/local/apache/bin/apxs选项，
其中apxs是在安装Apache时产生的，apxs是一个为Apache HTTP服务器编译和安装扩展模块的工具，
使之可以用由mod_so提供的LoadModule指令在运行时加载到Apache服务器中。
我的理解是通过这个工具把PHP模块动态加载到Apache中
出现错误:configure: error: xml2-config not found. Please check your libxml2 installation.
运行yum install libxml2，然后再运行yum install libxml2-devel安装完毕后，重新运行上面的./configure命令。
2. 编译
make   
3. 测试编译
make test   
4. 安装
make install
cp /usr/local/src/php-5.3.16/php.ini-development  /usr/local/php/lib/php.ini
把原来位于源代码里面的php.ini-development拷贝到/usr/local/php/lib/php.ini下，
并且重命名为php.ini
5、测试 写一个页面test.php,放在apache的web目录下，测试上面用命令创建的数据库
```bash
<?php 
$mysqli=new mysqli();
$mysqli->connect('localhost','root','123456','gywtest');
// 创建查询
$sqlstr='select * from student';
//发送查询给MySql
$result=$mysqli->query($sqlstr);
while($row=$result->fetch_object())
{ 
$name=$row->stuname;
echo $name;
}
```
安装CURL扩展
进入源码目录
```bash
cd php/
cd /ext/curl
make clean
/usr/local/php/bin/phpize
./configure    --with-php-config=/usr/local/php/bin/php-config   --with-curl
make && make install
查看  curl.so文件是否安装到这个目录下
ll /usr/local/php/lib/php/extensions/no-debug-non-zts-20060613/
vi /usr/local/php/lib/php.ini
extension_dir = "/usr/local/php5/lib/php/extensions/no-debug-non-zts-20060613/"；
添加如下一行：
extension=curl.so
查看是否加载了CURL扩展
/usr/local/php/bin/php -m
重启http
```

nginx 支持php环境
```bash
nginx最常用的方法是利用 tcp/ip 协议连接 phpfastcgi 接口, 因此要连接php必须先启动fastcgi程序.

启动方法：

1# /usr/local/bin/php-cgi-b 127.0.0.1:9000 -c /usr/local/php/lib/php.ini

2配置php-fpm启动
cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
cp init.d.php-fpm /etc/init.d/php-fpm
chmod a+x /etc/init.d/php-fpm
service php-fpm start


vim /usr/local/nginx/conf/nginx.conf
#增加 
location ~ \.php(.*)$  {
                root            /var/www;
                fastcgi_pass   127.0.0.1:9000;
                fastcgi_index  index.php;
                fastcgi_split_path_info  ^((?U).+\.php)(/?.+)$;
                fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
                fastcgi_param  PATH_INFO  $fastcgi_path_info;
                fastcgi_param  PATH_TRANSLATED  $document_root$fastcgi_path_info;
                include        fastcgi_params;
        }
```