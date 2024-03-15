# check\_memory.sh

> 检查可用内存，小于100M就报警并发邮件通知管理员

### 脚本主要内容

#### check\_memory.sh

```bash
#!/bin/bash

avalibleMemory=$(free -m | awk 'NR==2{print $NF}')
if [ $avalibleMemory -lt 100 ]; then
    echo -e "Warning: avalible memory is less than 100M\n\nValue: ${avalibleMemory}M\n\nTime: $(date '+%F %T')" | tee /tmp/checkMemory.log
    mail -s "Memory Warning $(date '+%F %T')" 121509661@qq.com < /tmp/checkMemory.log
else
    echo "Memory is enough."
fi
```

### 配置邮件服务

#### config\_mail.sh

```bash
#!/bin/bash

# configure mail service
echo "set from=anycing@sina.com smtp=smtp.sina.com smtp-auth-user=anycing smtp-auth-password=a7c7b1c1e2c87571dz smtp-auth=login" >> /etc/mail.rc

# start mail service
systemctl restart postfix
systemctl enable postfix
```

### 写入定时任务

```bash
echo "*/3 * * * * /bin/bash /server/scripts/check_memory.sh > /dev/null 2>&1" >> /var/spool/cron/root

chmod 600 /var/spool/cron/root
```
