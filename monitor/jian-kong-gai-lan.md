# 监控概览

### 常见监控命令

#### cpu、内存、硬盘、网络

* free
* df
* top
* htop (epel)
* uptime
* iftop
* iostat
* iotop
* vmstat
* netstat
* nethogs （每一个进程用了多少流量）

####

### 使用Shell脚本监控服务器

#### 内存监控

> 内存：每隔一分钟监控一次内存，当你的可用内存低于100M时发出邮件报警，要求显示剩余内存值

```bash
#!/bin/bash

while true
do
    free=`free -m | awk 'NR==2{print $NF}'`
    if [ $free -lt 100 ]
    then
        echo $free | mail -s "当前内存" anycing@qq.com
    fi
    sleep 60
do

ab -n 10000 -c 3 http://10.0.0.61/zabbix/index.php
```

