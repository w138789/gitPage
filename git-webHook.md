---
title: GIT-webHook配置
date: 2016-05-23 10:20:13
categories: git
---
根文件夹新建webHook.php
```bash
<?php
  $pwd = getcwd();
  $command = 'cd ' . $pwd . ' && git pull';
  $output = shell_exec($command);
  print $output;
?>
git 版本库添加http://siteurl/webHook.php
修改根文件夹用户为www
vim /usr/local/php/etc/php-fpm.conf
user = www
group = www
配置www用户具有git权限,确保git pull 能够正常使用
```
