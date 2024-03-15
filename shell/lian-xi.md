# 练习

### rsync服务启动脚本

{% hint style="success" %}
环境：CentOS6
{% endhint %}

#### rsyncd.sh

```
#!/bin/bash

# chkconfig: 2345 20 80

if [ $# -ne 1 ]; then
    echo "Usage: $0 {start|stop|restart}"
    exit 1
fi
if [ $1 == "start" ]; then
    if [ -s /var/run/rsyncd.pid ]; then
        echo "rsync is running already, pid: $(cat /var/run/rsyncd.pid)"
    else
        rsync --daemon
    fi
elif [ $1 == "stop" ]; then
    if [ ! -s /var/run/rsyncd.pid ]; then
        echo "rsync is not running"
    else
        kill $(cat /var/run/rsyncd.pid)
    fi
elif [ $1 == "restart" ]; then
    if [ ! -s /var/run/rsyncd.pid ]; then
        rsync --daemon
    else
        kill $(cat /var/run/rsyncd.pid)
        sleep 2
        rsync --daemon
    fi
else
    echo "Usage: $0 {start|stop|restart}"
fi
```

#### 让脚本支持service启动

```
# 脚本加执行权限
chmod +x /server/scripts/rsyncd.sh

# 脚本移至 /etc/init.d/ 目录，并去掉扩展名
mv /server/scripts/rsyncd.sh /etc/init.d/rsyncd

# 添加至服务（前提脚本中有注释项：# chkconfig: 2345 20 80 ）
chkconfig --add rsyncd
```

#### 测试服务启动

```
service rsyncd start

service rsyncd stop

service rsyncd restart
```

```
chkconfig rsyncd off

chkconfig rsyncd on

chkconfig --level 45 rsyncd off

chkconfig --level 45 rsyncd on
```



### Nginx官方服务文件

{% hint style="success" %}
环境：CentOS7
{% endhint %}

```
cat > /usr/lib/systemd/system/nginx.service << EOF 
[Unit]
Description=nginx - high performance web server
Documentation=http://nginx.org/en/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target
EOF
```



### 判断网站访问状态

```
#!/bin/bash

[ -e /etc/init.d/functions ] && . /etc/init.d/functions

checkUrl() {
    wget -q $1 &> /dev/null
    retval=$?

    if [ $retval -eq 0 ]; then
        action "Test host" /bin/true
    else
        action "Test host" /bin/false
    fi
}

main() {
    checkUrl $1
}

main $*
```



### 选择要安装的服务

```
#!/bin/bash

printMenu() {
    cat << EOMENU
┌─────┬─────────┐
│ num │ service │
├─────┼─────────┤
│  1  │  Nginx  │
├─────┼─────────┤
│  2  │  MySQL  │
├─────┼─────────┤
│  3  │   PHP   │
└─────┴─────────┘
EOMENU
}

chooseItem() {
    read -p "Please enter a number: " num

    case "$num" in
        1)
            echo "You will install Nginx"
        ;;
        2)
            echo "You will install MySQL"
        ;;
        3)
            echo "You will install PHP"
        ;;
        *)
            echo "Please enter {1|2|3}"
        ;;
    esac
}

printMenu
chooseItem
```



### 添加颜色输出

```
function addColor() {
    case "$2" in
        black)
            echo -e "\033[30m$1\033[0m"
        ;;
        red)
            echo -e "\033[31m$1\033[0m"
        ;;
        green)
            echo -e "\033[32m$1\033[0m"
        ;;
        brown)
            echo -e "\033[33m$1\033[0m"
        ;;
        blue)
            echo -e "\033[34m$1\033[0m"
        ;;
        magenta)
            echo -e "\033[35m$1\033[0m"
        ;;
        cyan)
            echo -e "\033[36m$1\033[0m"
        ;;
        white)
            echo -e "\033[37m$1\033[0m"
        ;;
    esac
}

main() {
    addColor $1 $2
}

main $*
```
