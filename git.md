---
title: GIT配置
date: 2016-05-23 10:20:13
categories: git
---
1.安装完成后，还需要最后一步设置，在命令行输入：
//输入账号和邮箱
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
2.创建版本库
$ mkdir learngit
$ cd learngit
$ pwd
$ git init

言归正传，现在我们编写一个readme.txt文件，内容如下：
Git is a version control system.
Git is free software.

第一步，用命令git add告诉Git，把文件添加到仓库：
$ git add readme.txt

第二步，用命令git commit告诉Git，把文件提交到仓库：
$ git commit -m "wrote a readme file"

2.添加到过程库
$ git remote add origin git@github.com:w138789/learngit.git

下一步，就可以把本地库的所有内容推送到远程库上：
$ git push -u origin master
此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；

//强制覆盖本地文件
git fetch --all  
git reset --hard origin/master 
git pull

Git 配置代理， 加速 git clone
1. 首先，本地挂好代理工具，不多说。默认情况下端口是 1080。
2. 在 Git 命令行输入以下配置全局代理：
```bash
//127.0.0.1指的是本地，1080指的是代理工具设置的端口
git config --global http.proxy socks5://127.0.0.1:1080
```
3. 使用以下命令查看是否配置好
```bash
git config --global -e
查看到有即成功
[http]
        proxy = socks5://127.0.0.1:1080
```
4. 取消代理使用命令：
```bash
git config --global --unset http.proxy
```
