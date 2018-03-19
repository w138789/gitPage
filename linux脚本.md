---
title: linux脚本
date: 2018-01-08 09:35:16
categories: linux
tags:
---

备份线上数据库到本地
```bash
#!/bin/bash

echo start: $(date '+%Y%m%d %H:%M:%S') >> /home/su/log.log

# 匹配提示符
CMD_PROMPT="\](\$|#)"

passwords="laobing7026546"

expect<<-END
set timeout 5
send_user staring\r
    spawn /usr/bin/scp laobing@120.78.81.67:/home/laobing/mysqlDump/*.sql.gz /home/su/databaseBak/
    expect -re $CMD_PROMPT
    send -- $passwords\r
    expect -re $CMD_PROMPT
set timeout 6000
    expect eof
exit
END

rename 's/\:/-/' /home/su/databaseBak/*.sql.gz
rename 's/\:/-/' /home/su/databaseBak/*.sql.gz
cp /home/su/databaseBak/*.sql.gz /mnt/d/work/老兵/数据库/bak/temporary/
rm -rf /home/su/databaseBak/*.sql.gz

echo end: $(date '+%Y%m%d %H:%M:%S') >> /home/su/log.log
```

免输入密码启动服务
```bash
#!/bin/bash
echo 123456 | sudo -S service php7.1-fpm start
echo 123456 | sudo -S service mysql start
echo 123456 | sudo -S service nginx start
echo 123456 | sudo -S service cron start
```

备份远程库到本地
```bash
#!/bin/bash

# 匹配提示符
CMD_PROMPT="\](\$|#)"

passwords="laobing7026546"

packageName="laobing_"`date +%Y%m%d`"_"`date +%H`":"`date +%M`":*.sql"

expect<<-END
set timeout 1
send_user staring\r
spawn /usr/bin/ssh laobing@120.78.81.67 '/home/laobing/bash/mysqlLaobing.sh'
expect -re $CMD_PROMPT
    send -- $passwords\r
    expect -re $CMD_PROMPT
set timeout 6000
    expect eof
exit
END

expect<<-END
set timeout 1
send_user staring\r
    spawn /usr/bin/scp laobing@120.78.81.67:/home/laobing/mysqlDump/${packageName}".gz" /home/su/databaseBak/
    expect -re $CMD_PROMPT
    send -- $passwords\r
    expect -re $CMD_PROMPT
set timeout 6000
    expect eof
exit
END

gzip -df /home/su/databaseBak/${packageName}".gz"

mysql -u root -p123456 laobing < /home/su/databaseBak/${packageName}
```