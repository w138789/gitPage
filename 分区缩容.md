---
title: 分区缩容及home独立分区
date: 2019-03-081.查看我们机器现有的分区状况
 10:20:13
categories: others
---
~~~bash
一. 分区缩容
1.查看我们机器现有的分区状况
su@su:~$ df -h
文件系统        容量  已用  可用 已用% 挂载点
udev            7.8G     0  7.8G    0% /dev
tmpfs           1.6G  3.0M  1.6G    1% /run
/dev/sdc1        53G   11G   40G   21% /
/dev/sdc3        60G   33G   25G   58% /home
/dev/sdc2       123G  115G  8.6G   93% /media/su/bak

2. 卸载分区
su@su:~$ umount /home

3. 查看分区
su@su:~$ df -h
文件系统        容量  已用  可用 已用% 挂载点
udev            7.8G     0  7.8G    0% /dev
tmpfs           1.6G  3.0M  1.6G    1% /run
/dev/sdc1        53G   11G   40G   21% /
/dev/sdc2       123G  115G  8.6G   93% /media/su/bak

4. 修复分区 (/dev/sdc3 为要缩小的分区)
su@su:~$ e2fsck -f /dev/sdc3

5. 缩小分区到40G
su@su:~$ resize2fs -p /dev/sdc3 40G

6. 加载分区
su@su:~$ mount /home
文件系统        容量  已用  可用 已用% 挂载点
udev            7.8G     0  7.8G    0% /dev
tmpfs           1.6G  3.0M  1.6G    1% /run
/dev/sdc1        53G   11G   40G   21% /
/dev/sdc3        40G   33G   7G   58% /home
/dev/sdc2       123G  115G  8.6G   93% /media/su/bak

7. 用GParted软件进行分区
选中分区->右键->卸载分区->更改大小/移动   (有可能软件会崩溃,操作一步点击头部应用一步)

二. home独立分区

1. 创建中间挂载点
su@su:~$ mkdir /media/home

2. 建立中间挂载
su@su:~$ mount /dev/sdc3 /media/home

3. 复制home文件夹到新分区
su@su:~$ cp -a /home/* /media/home

3. 将新的分区挂载到/home,并写入到配置文件中，最好使用UUID
su@su:~$ vim /etc/fstab

	:r!blkid /dev/sdc3

#主要取UUID 其他的手动填写

# /media/home
UUID=84209ef0-fbb3-4173-9757-30a87d220866       /home           ext4            defaults 0 0

#以下为另一个分区
# /media/bak
UUID=4701F89E256BD1BB                           /media/su/bak   ntfs            defaults 0 0

:wq

4. 让/etc/fstab文件生效
su@su:~$ mount -a

5. 重启看是否正常访问
~~~