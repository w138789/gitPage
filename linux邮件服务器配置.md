---
title: linux邮件服务器配置
date: 2017-03-09 10:20:13
categories: linux
---

一. 配置邮件发信服务器postfix
```bash
vim /etc/postfix/main.cf
myhostname = mail.su.com
mydomain = su.com
myorigin = $myhostname
myorigin = $mydomain
inet_interfaces = all
mydestination = $myhostname, $mydomain
mynetworks = 192.168.83.0/28, 127.0.0.0/8
:wq
service postfix restart
```
测试发信服务器是否成功安装
[root@192 /]# telnet 192.168.83.149 25
Trying 192.168.83.149...
Connected to 192.168.83.149.
Escape character is '^]'.
220 mail.su.com ESMTP Postfix
mail from:su@su.com
250 2.1.0 Ok
rcpt to:root@su.com
250 2.1.5 Ok
data
354 End data with <CR><LF>.<CR><LF>
dd
.
250 2.0.0 Ok: queued as A11FCA32D7
quit
221 2.0.0 Bye
表示已成功发邮件

二. 配置邮件收信服务器dovecot
```bash
yum install -y dovecot
vim /etc/dovecot/dovecot.conf
protocols = imap pop3 lmtp
:wq
vim /etc/dovecot/conf.d/10-auth.conf 
disable_plaintext_auth = no
:wq
vim /etc/dovecot/conf.d/10-mail.conf 
mail_location = mbox:~/mail:INBOX=/var/mail/%u
:wq
useradd su1
passwd su1
useradd su2
passwd su2
```

错误解决办法
```bash
[root@192 /]# telnet 192.168.83.149 110
Trying 192.168.83.149...
Connected to 192.168.83.149.
Escape character is '^]'.
+OK Dovecot ready.
user su1
+OK
pass 123
-ERR [IN-USE] Couldn't open INBOX: Internal error occurred. Refer to server log for more information. [2017-03-09 04:41:38]
Connection closed by foreign host.

解决办法
mkdir -p /home/su1/mail/.imap/INBOX
mkdir -p /home/su2/mail/.imap/INBOX
```

测试是否成功安装
```bash
[root@192 /]# telnet 192.168.83.149 110
Trying 192.168.83.149...
Connected to 192.168.83.149.
Escape character is '^]'.
+OK Dovecot ready.
user su1
+OK
pass 123
+OK Logged in.
list
+OK 1 messages:
1 398
.
quit
+OK Logging out.
Connection closed by foreign host.
```
表示成功收信






