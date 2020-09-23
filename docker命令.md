---
title: docker命令
date: 2020-09-23 10:20:13
categories: docker
---
mysql服务远程连接
#运行容器
```bash
docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
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
```