# Project

## 备份web与nfs服务器

> 所有服务器的备份目录都为 /backup
>
> 要备份的系统配置文件包括但不限于如下（没有目录则手动创建）：
>
> &#x20;       定时任务服务的配置文件： /var/spool/cron/root
>
> &#x20;       开机自启动的配置文件： /etc/rc.local
>
> &#x20;       日常脚本的目录： /server/scripts
>
> Web服务器目录： /var/www/html
>
> Web服务器日志： /app/logs
>
> Web服务器本地保留打包后7天的备份数据即可
>
> 备份服务器 backup 上保留最近7天的备份数据，同时保留6个月内每周一的所有数据副本
>
> 备份服务器上按照备份服务器上的内网IP为目录保存备份，备份的文件按照时间名字保存
>
> 每天00:00进行备份，早上8:00 把备份成功或失败的结果信息发送至系统管理员邮箱

| Server    | IP             | Hostname |
| --------- | -------------- | -------- |
| Nginx Web | 172.16.1.17/24 | web01    |
| NFS       | 172.16.1.31/24 | nfs01    |
| Rsync     | 172.16.1.41/24 | backup   |



### web01

```
cat /server/scripts/backup.sh 

#!/bin/bash

export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin"
export RSYNC_PASSWORD=rsync_password

IP=$(hostname -i)
HostName=$(hostname)
mkdir -p /backup/$IP

# tar打包
tar -zchf /backup/${IP}/${HostName}_$(date "+%F_%w").tar.gz /var/spool/cron/root /etc/rc.local /server/scripts /var/www/html /app/logs &> /dev/null &&\

touch /backup/${IP}/${HostName}_$(date "+%F_%w").flag &&\

# md5采集
find /backup/${IP}/${HostName}_$(date "+%F_%w").tar.gz | xargs md5sum > /backup/${IP}/${HostName}_$(date "+%F_%w").flag &&\

# delete
find /backup/${IP}/ -type f -name "*.tar.gz" -mtime "+7" | xargs rm -f &&\

# push
rsync -az /backup/ rsync_backup@172.16.1.41::backup &> /dev/null
```

```
crontab -e

#------ backup ------#
00 00 * * * /bin/bash /server/scripts/backup.sh > /dev/null 2>&1

crontab -l
```



### nfs01

```
cat /server/scripts/backup.sh 

#!/bin/bash

export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin"
export RSYNC_PASSWORD=rsync_password

IP=$(hostname -i)
HostName=$(hostname)
mkdir -p /backup/$IP

# tar打包
tar -zchf /backup/${IP}/${HostName}_$(date "+%F_%w").tar.gz /var/spool/cron/root /etc/rc.local /server/scripts &> /dev/null &&\

touch /backup/${IP}/${HostName}_$(date "+%F_%w").flag &&\

# md5采集
find /backup/${IP}/${HostName}_$(date "+%F_%w").tar.gz | xargs md5sum > /backup/${IP}/${HostName}_$(date "+%F_%w").flag &&\

# delete
find /backup/${IP}/ -type f -name "*.tar.gz" -mtime "+7" | xargs rm -f &&\

# push
rsync -az /backup/ rsync_backup@172.16.1.41::backup &> /dev/null
```

```
crontab -e

#------ backup ------#
00 00 * * * /bin/bash /server/scripts/backup.sh > /dev/null 2>&1

crontab -l
```



### backup

```
cat /server/scripts/delete.sh

#!/bin/bash

export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin"

# delete
find /backup/ -type f -name "*.tar.gz" -mtime "+180" | xargs rm -f &&\

find /backup/ -type f ! -name "*_1.tar.gz" -mtime "+7" | xargs rm -f

# check md5
find /backup/ -type f -name "*$(date "+%F_%w").flag" | xargs md5sum -c > /tmp/backup_message.log

# send email
mail -s "Backup Message $(date "+%F)" anycing@qq.com < /tmp/backup_message.log
```

```
vim /etc/mail.rc

set from=anycing@qq.com smtp=smtp.qq.com smtp-auth-user=anycing smtp-auth-password=xxxxuhuqwgcwxxxx smtp-auth=login

systemctl restart postfix

systemctl enable postfix
```

```
crontab -e

#------ backup ------#
00 08 * * * /bin/bash /server/scripts/delete.sh > /dev/null 2>&1

crontab -l
```

