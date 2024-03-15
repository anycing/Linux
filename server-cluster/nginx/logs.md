# logs

### Nginx日志变量

| 变量名 | 说明 |
| --- | -- |
| 1   |    |

### 日志配置格式

```
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

access_log  /var/log/nginx/access.log  main;
```

### 日志切割脚本

```
#!/bin/bash

DateFormat=$(date +%Y%m%d -d -1day)
BaseDir="/usr"
NginxLogsDir="/var/log/nginx/"
LogName="access"


[ -d $NginxLogsDir ] && cd $NginxLogsDir || exit 1
[ -f ${LogName}.log ] || exit 1

/bin/mv ${LogName}.log ${DateFormat}_${LogName}.log

$BaseDir/sbin/nginx -s reload
```

### 日志切割工具

```
yum install logrotate -y
```
