# inotify

{% hint style="warning" %}
**inotify 服务需要在Rsync基础上搭建，以下步骤在客户端上完成**
{% endhint %}

### 检查是否支持

```
uname -r

# 内核版本高于2.6


ls -l /proc/sys/fs/inotify/

-rw-r--r-- 1 root root 0 Dec  2 13:09 max_queued_events
-rw-r--r-- 1 root root 0 Dec  2 13:09 max_user_instances
-rw-r--r-- 1 root root 0 Dec  2 13:09 max_user_watches

# 目录中有这三个文件

# 则代表支持inotify
```



### 安装 inotify

```
yum install inotify-tools -y

# 需安装 epel 源
```

```
inotifywait -mrq --format '%w%f' -e create,close_write,delete /data

# “手动”对 /data 目录进行 “增、删、改” 事件的监控


inotifywait -mrq --timefmt '%Y-%m-%d %H:%M:%S' --format '%w%f - %T : %e' -e create,close_write,delete /data

# --timefmt 指定时间显示格式，
# --format 
#     %w 工作目录
#     %f 文件名称
#     %T 时间，具体格式由 --timefmt 指定
#     %e 事件
# -e create在实际工作中可省略
```



### 针对企业优化

```
echo "5000000" > /proc/sys/fs/inotify/max_user_watches

echo "327679" > /proc/sys/fs/inotify/max_queued_events


重启可能失效，则需要加入 /etc/rc.d/rc.local 中
```



### 配合脚本实战

```
# 编写 Shell 脚本实现对目录 /data 进行监控


vim /server/scripts/monitor.sh

#!/bin/bash

command="/usr/bin/inotifywait"

$command -mrq --format '%w%f' -e close_write,delete /data|\
while read line
do
    # 删除事件发生
    [ ! -e  "$line" ] && cd /data &&\
    rsync -az --delete ./ rsync_backup@172.16.1.41::backup && continue
    # 处理增改事件
    rsync -az --delete $line rsync_backup@172.16.1.41::backup
done
```

```
# 写入开机启动

vim /etc/rc.d/rc.local

/bin/bash /server/scripts/monitor.sh &
```

