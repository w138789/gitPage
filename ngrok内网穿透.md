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

5搭建自有服务器
注意: 需要两个域名: sujianxun.com ngrok.sujianxun.com 两个域名必需指向服务器
建insert.sh
```bash
#!/bin/bash
# -*- coding: UTF-8 -*-
#############################################
#作者网名：Javen
#修改:https://github.com/sunnyos/ngrok        #
#############################################
# 获取当前脚本执行路径
SELFPATH=$(cd "$(dirname "$0")"; pwd)
GOOS=`go env | grep GOOS | awk -F\" '{print $2}'`
GOARCH=`go env | grep GOARCH | awk -F\" '{print $2}'`
echo '请输入一个域名'
read DOMAIN
install_yilai(){
    yum -y install zlib-devel openssl-devel perl hg cpio expat-devel gettext-devel curl curl-devel perl-ExtUtils-MakeMaker hg wget gcc gcc-c++ unzip
}

# 安装git
install_git(){
    unstall_git
    if [ ! -f $SELFPATH/git-2.9.3.tar.gz ];then
        wget https://www.kernel.org/pub/software/scm/git/git-2.9.3.tar.gz
    fi
    tar zxvf git-2.9.3.tar.gz
    cd git-2.9.3
    ./configure --prefix=/usr/local/git
    make
    make install
    ln -s /usr/local/git/bin/* /usr/bin/
    rm -rf $SELFPATH/git-2.9.3
}

# 卸载git
unstall_git(){
    rm -rf /usr/local/git
    rm -rf /usr/local/git/bin/git
    rm -rf /usr/local/git/bin/git-cvsserver
    rm -rf /usr/local/git/bin/gitk
    rm -rf /usr/local/git/bin/git-receive-pack
    rm -rf /usr/local/git/bin/git-shell
    rm -rf /usr/local/git/bin/git-upload-archive
    rm -rf /usr/local/git/bin/git-upload-pack
}


# 安装go
install_go(){
    cd $SELFPATH
    uninstall_go
    # 动态链接库，用于下面的判断条件生效
    ldconfig
    # 判断操作系统位数下载不同的安装包
    if [ $(getconf WORD_BIT) = '32' ] && [ $(getconf LONG_BIT) = '64' ];then
        # 判断文件是否已经存在
        if [ ! -f $SELFPATH/go1.4.2.linux-amd64.tar.gz ];then
            wget https://www.golangtc.com/static/go/1.4.2/go1.9.2.linux-amd64.tar.gz
        fi
        tar zxvf go1.4.2.linux-amd64.tar.gz
    else
        if [ ! -f $SELFPATH/go1.4.2.linux-386.tar.gz ];then
            wget http://www.golangtc.com/static/go/1.4.2/go1.4.2.linux-386.tar.gz
        fi
        tar zxvf go1.4.2.linux-386.tar.gz
    fi
    mv go /usr/local/
    ln -s /usr/local/go/bin/* /usr/bin/
}

# 卸载go

uninstall_go(){
    rm -rf /usr/local/go
    rm -rf /usr/bin/go
    rm -rf /usr/bin/godoc
    rm -rf /usr/bin/gofmt
}

# 安装ngrok
install_ngrok(){
    uninstall_ngrok
    cd /usr/local
    if [ ! -f /usr/local/ngrok.zip ];then
        cd /usr/local/
        wget http://www.sunnyos.com/ngrok.zip
    fi
    unzip ngrok.zip
    export GOPATH=/usr/local/ngrok/
    export NGROK_DOMAIN=$DOMAIN
    cd ngrok
    openssl genrsa -out rootCA.key 2048
    openssl req -x509 -new -nodes -key rootCA.key -subj "/CN=$NGROK_DOMAIN" -days 5000 -out rootCA.pem
    openssl genrsa -out server.key 2048
    openssl req -new -key server.key -subj "/CN=$NGROK_DOMAIN" -out server.csr
    openssl x509 -req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out server.crt -days 5000
    cp rootCA.pem assets/client/tls/ngrokroot.crt
    cp server.crt assets/server/tls/snakeoil.crt
    cp server.key assets/server/tls/snakeoil.key
    # 替换下载源地址
    sed -i 's#code.google.com/p/log4go#github.com/keepeye/log4go#' /usr/local/ngrok/src/ngrok/log/logger.go
    cd /usr/local/go/src
    GOOS=$GOOS GOARCH=$GOARCH ./make.bash
    cd /usr/local/ngrok
    GOOS=$GOOS GOARCH=$GOARCH make release-server
    /usr/local/ngrok/bin/ngrokd -domain=$NGROK_DOMAIN -httpAddr=":80"
}

# 卸载ngrok
uninstall_ngrok(){
    rm -rf /usr/local/ngrok
}

# 编译客户端
compile_client(){
    cd /usr/local/go/src
    GOOS=$1 GOARCH=$2 ./make.bash
    cd /usr/local/ngrok/
    GOOS=$1 GOARCH=$2 make release-client
}

# 生成客户端
client(){
    echo "1、Linux 32位"
    echo "2、Linux 64位"
    echo "3、Windows 32位"
    echo "4、Windows 64位"
    echo "5、Mac OS 32位"
    echo "6、Mac OS 64位"
    echo "7、Linux ARM"

    read num
    case "$num" in
        [1] )
            compile_client linux 386
        ;;
        [2] )
            compile_client linux amd64
        ;;
        [3] )
            compile_client windows 386
        ;;
        [4] ) 
            compile_client windows amd64
        ;;
        [5] ) 
            compile_client darwin 386
        ;;
        [6] ) 
            compile_client darwin amd64
        ;;
        [7] ) 
            compile_client linux arm
        ;;
        *) echo "选择错误，退出";;
    esac

}


echo "请输入下面数字进行选择"
echo "#############################################"
echo "#作者网名：Javen"
echo "#############################################"
echo "------------------------"
echo "1、全新安装"
echo "2、安装依赖"
echo "3、安装git"
echo "4、安装go环境"
echo "5、安装ngrok"
echo "6、生成客户端"
echo "7、卸载"
echo "8、启动服务"
echo "9、查看配置文件"
echo "------------------------"
read num
case "$num" in
    [1] )
        install_yilai
        install_git
        install_go
        install_ngrok
    ;;
    [2] )
        install_yilai
    ;;
    [3] )
        install_git
    ;;
    [4] )
        install_go
    ;;
    [5] )
        install_ngrok
    ;;
    [6] )
        client
    ;;
    [7] )
        unstall_git
        uninstall_go
        uninstall_ngrok
    ;;
    [8] )
        
        echo "启动端口"
        read port
        /usr/local/ngrok/bin/ngrokd -domain=$DOMAIN -httpAddr=":$port"
    ;;
    [9] )
        echo "#############################################"
        echo "#作者网名：Javen"
        echo "#创建ngrok.cfg文件并添加以下内容"
        echo server_addr: '"'$DOMAIN:4443'"'
        echo "trust_host_root_certs: false"
        echo "#############################################"
        echo "#############################################"
        echo "#Window启动脚本"
        echo "ngrok -config=ngrok.cfg -subdomain=你域名的前缀  本地映射的端口号"
        echo "ngrok -config=ngrok.cfg -subdomain=javen  80"
        echo "#############################################"
        echo "#############################################"
        echo "#Linux Mac 启动脚本"
        echo "./ngrok -config=./ngrok.cfg -subdomain=你域名的前缀  本地映射的端口号"
        echo "./ngrok -config=./ngrok.cfg -subdomain=javen  80"
        echo "#Linux Mac 后台启动脚本"
        echo "setsid ./ngrok -config=./ngrok.cfg -subdomain=javen 80"
        echo "#############################################"
    ;;
    *) echo "";;
esac
```
输入域名 sujianxun.com

全新安装

生成客户端

服务端启动:
```bash
vimstart.sh 

setsid /usr/local/ngrok/bin/ngrokd -domain=sujianxun.com -httpAddr=":8000"
```
nginx转发到8000端口,客户端直接访问80端口
```bash
vim ngrok.conf

upstream phpsu {
    server 127.0.0.1:8000;
    keepalive 64;
}
server {
    listen 80;
    server_name ngrok.sujianxun.com;
    access_log /var/log/nginx/ngrok.log;
    location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host  $http_host:8000;
        proxy_set_header X-Nginx-Proxy true;
        proxy_set_header Connection "";
        proxy_pass      http://phpsu;
    }
}
```

客户端启动:

下载客户端
```bash
vim ngrok.cfg

server_addr: "sujianxun.com:4443"
trust_host_root_certs: false

启动客户端
nohup /mnt/d/ngrok/su/ngrok -config=/mnt/d/ngrok/su/ngrok.cfg -subdomain=ngrok 80 &
```

6 ngrok.cc 服务器搭建
```bash
下载说明地址: https://www.sunnyos.com/article-show-74.html
linux 服务端启动
./ngrokd --domain="xx.xxx.xx.xx"  默认方式
setsid ./ngrokd --domain="xx.xxx.xx.xx" 后台运行
客户端启动:
(1)建配置文件
建ngrok.cfg

server_addr: "server.ngrok.cc:4443"
    tunnels:
    sunny:
        subdomain: "sunnyos"
        auth: "sunny:admin1993"
        proto:
            http: 192.168.1.108:80
    sunny1:
        subdomain: "sunnyos1"
        proto:
            http: 192.168.1.104:80
    ssh:
        remote_port: 50000
        proto:
            tcp: 192.168.1.120:22

./ngrok -config=./ngrok.cfg start sunny

(2)直接启动
./ngrok -server_addr=104.225.146.172:4443 -proto=tcp 22 这样映射本地的22端口，远程端口将由服务器分配
./ngrok -server_addr=104.225.146.172:4443 -subdomain=abc -proto=http 80 映射前置域名为abc的域名到本地的80端口

```