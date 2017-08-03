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

安装nginx
tar  -zxvf nginx-1.13.0.tar.gz
cd /nginx-1.13.0
./configure --prefix=/usr/local/nginx
make && make install
配置开机启动脚本
```bash
vim /etc/init.d/nginx

#!/bin/sh
#
# nginx - this script starts and stops the nginx daemon
#
# chkconfig:   - 85 15
# description:  Nginx is an HTTP(S) server, HTTP(S) reverse \
#               proxy and IMAP/POP3 proxy server
# processname: nginx
# config:      /etc/nginx/nginx.conf
# config:      /etc/sysconfig/nginx
# pidfile:     /var/run/nginx.pid

# Source function library.
. /etc/rc.d/init.d/functions

# Source networking configuration.
. /etc/sysconfig/network

# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0

nginx="/usr/local/nginx/sbin/nginx"
prog=$(basename $nginx)

sysconfig="/etc/sysconfig/$prog"
lockfile="/var/lock/subsys/nginx"
pidfile="/usr/local/nginx/logs/${prog}.pid"

NGINX_CONF_FILE="/usr/local/nginx/conf/nginx.conf"

[ -f $sysconfig ] && . $sysconfig


start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}

stop() {
    echo -n $"Stopping $prog: "
    killproc -p $pidfile $prog
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}

restart() {
    configtest_q || return 6
    stop
    start
}

reload() {
    configtest_q || return 6
    echo -n $"Reloading $prog: "
    killproc -p $pidfile $prog -HUP
    echo
}

configtest() {
    $nginx -t -c $NGINX_CONF_FILE
}

configtest_q() {
    $nginx -t -q -c $NGINX_CONF_FILE
}

rh_status() {
    status $prog
}

rh_status_q() {
    rh_status >/dev/null 2>&1
}

# Upgrade the binary with no downtime.
upgrade() {
    local oldbin_pidfile="${pidfile}.oldbin"

    configtest_q || return 6
    echo -n $"Upgrading $prog: "
    killproc -p $pidfile $prog -USR2
    retval=$?
    sleep 1
    if [[ -f ${oldbin_pidfile} && -f ${pidfile} ]];  then
        killproc -p $oldbin_pidfile $prog -QUIT
        success $"$prog online upgrade"
        echo 
        return 0
    else
        failure $"$prog online upgrade"
        echo
        return 1
    fi
}

# Tell nginx to reopen logs
reopen_logs() {
    configtest_q || return 6
    echo -n $"Reopening $prog logs: "
    killproc -p $pidfile $prog -USR1
    retval=$?
    echo
    return $retval
}

case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart|configtest|reopen_logs)
        $1
        ;;
    force-reload|upgrade) 
        rh_status_q || exit 7
        upgrade
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    status|status_q)
        rh_$1
        ;;
    condrestart|try-restart)
        rh_status_q || exit 7
        restart
	    ;;
    *)
        echo $"Usage: $0 {start|stop|reload|configtest|status|force-reload|upgrade|restart|reopen_logs}"
        exit 2
esac

:wq
chmod 755 nginx
chkconfig --add nginx
chkconfig nginx on
```

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
vim /etc/profile
PATH=$PATH:/usr/local/mysql/bin
export PATH
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
--with-gd \
--with-jpeg-dir \
--with-freetype-dir=/usr/include/freetype2/freetype/ \
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
安装gd扩展
```bash
1.进入相应外部库文件位置
vim /root/php-5.6.9/ext/gd  

提示：在源码包的ext目录下会有很多外部库文件的源码，不知道是不是全部都在那里面。
2.安装GD库相应的依赖

1.安装vpx
yum install -y libvpx libvpx-devel  

2.安装jpeg 
yum install -y libjpeg-turbo libjpeg-turbo-devel  

3.安装png 
yum install -y libpng libpng-devel  

4.安装fretype
yum install -y freetype freetype-devel  

提示：我用的是阿里云的源，这些软件都可以找到，如果不行的话请自行切换


3.配置编译GD库
/usr/local/php/bin/phpize && ./configure --with-php-config=/usr/local/php/bin/php-config --with-vpx-dir=/usr/local/ --with-jpeg-dir=/usr/local/  --with-png-dir=/usr/local/ --with-freetype-dir=/usr/local && make && make install

添加如下一行：
vim /usr/local/php/lib/php.ini
extension=gd.so
```
如果不行重新编绎安装php 重新安装前先make clean

安装mcrypt扩展
```bash
1 先去http://www.sourceforge.net下载Libmcrypt,mhash,mcrypt安装包 ,下面是我找到的链接

libmcrypt-2.5.8.tar.gz
下载地址: http://sourceforge.net/project/showfiles.php?group_id=87941&package_id=91774&release_id=487459
mhash-0.9.9.tar.gz
下载地址: http://sourceforge.net/project/showfiles.php?group_id=4286&package_id=4300&release_id=645636
mcrypt-2.6.8.tar.gz
下载地址: http://sourceforge.net/project/showfiles.php?group_id=87941&package_id=91948&release_id=642101

2 先安装Libmcrypt

#tar -zxvf libmcrypt-2.5.8.tar.gz

   #cd libmcrypt-2.5.8

   #./configure

   #make

   #make install 说明：libmcript默认安装在/usr/local

3 安装mhash

   #tar -zxvf mhash-0.9.9.9.tar.gz

   #cd mhash-0.9.9.9

   #./configure

   #make

   #make install

4 安装mcrypt

   #tar -zxvf mcrypt-2.6.8.tar.gz

   #cd mcrypt-2.6.8

   #LD_LIBRARY_PATH=/usr/local/lib ./configure

   #make

   #make install
5 安装php扩展
cd /root/php-5.6.3/ext/mcrypt
/usr/local/php/bin/phpize
./configure
make && make install
添加*.so加载
vim /usr/local/php/lib/php.ini
extension=mcrypt.so
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
//php7.1安装注意:
cp /usr/local/php-7.1.1/etc/php-fpm.d/www.conf.default /usr/local/php-7.1.1/etc/php-fpm.d/www.conf


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
