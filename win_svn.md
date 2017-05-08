---
title: WIN SVN配置
date: 2016-05-23 10:20:13
categories: win_svn
---
1，新建一个目录用于存储SVN所有文件 
mkdir D:/svn
2，新建一个版本仓库 
svnadmin create D:/svn/repository
3.
打开 svnserve.conf
[general]
anon-access = none
auth-access = write
password-db = passwd
4.
打开 passwd
[users]
su =123456
su1 = 654321
5.启动服务器
sc create svnserve binPath= "D:\Program Files (x86)\Subversion\bin\svnserve.exe --service --root E:/svn/cheshi" DisplayName= "svnserve" depend= tcpip start= auto
6.钩子同步
在hooks文件夹下建立post-commit.bat
SET REPOS=%1
SET USER=%2
SET SVN="D:\Program Files (x86)\Subversion\bin\svn.exe"
SET DIR="D:\cheshi"
(call %SVN% update %DIR% --username su --password 7026546 --non-interactive)
7.同步检出到文件夹
svn co svn://114.215.86.206/su D:/WWW/zhongfang