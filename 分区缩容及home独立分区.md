---
title: 分区缩容及home独立分区
date: 2019-03-081.查看我们机器现有的分区状况
 10:20:13
categories: others
---
~~~bash

1.查看我们机器现有的分区状况
su@su:~/www/santai$ df -h
文件系统        容量  已用  可用 已用% 挂载点
udev            7.8G     0  7.8G    0% /dev
tmpfs           1.6G  3.0M  1.6G    1% /run
/dev/sdc1        53G   11G   40G   21% /
/dev/sdc3        60G   33G   25G   58% /home
/dev/sdc2       123G  115G  8.6G   93% /media/su/bak

2. 卸载分区
su@su:~/www/santai$ umount /home

3. 查看分区
su@su:~/www/santai$ df -h
文件系统        容量  已用  可用 已用% 挂载点
udev            7.8G     0  7.8G    0% /dev
tmpfs           1.6G  3.0M  1.6G    1% /run
/dev/sdc1        53G   11G   40G   21% /
/dev/sdc2       123G  115G  8.6G   93% /media/su/bak

4. 修复分区 (/dev/sdc3 为要缩小的分区)
su@su:~/www/santai$ e2fsck -f /dev/sdc3

5. 缩小分区到40G
su@su:~/www/santai$ resize2fs -p /dev/sdc3 40G

6. 加载分区
su@su:~/www/santai$ mount /home
文件系统        容量  已用  可用 已用% 挂载点
udev            7.8G     0  7.8G    0% /dev
tmpfs           1.6G  3.0M  1.6G    1% /run
/dev/sdc1        53G   11G   40G   21% /
/dev/sdc3        40G   33G   7G   58% /home
/dev/sdc2       123G  115G  8.6G   93% /media/su/bak

7. 用GParted软件进行分区
选中分区->右键->卸载分区->更改大小/移动   (有可能软件会崩溃,操作一步点击头部应用一步)


~~~