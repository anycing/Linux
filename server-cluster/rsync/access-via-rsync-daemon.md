# Access via rsync daemon

{% hint style="success" %}
**SERVER: backup （以下配置在服务端backup中完成）**
{% endhint %}

### 配置文件设置

```
rpm -q rsync

yum install -y rsync

cp /etc/rsyncd.conf{,.ori_backup}
```

```
cat > /etc/rsyncd.conf << EOF
# rsync_config______start
uid = rsync
gid = rsync
fake super = yes
use chroot = no
max connections = 200
timeout = 600
pid file = /var/run/rsyncd.pid
lock file = /var/run/rsync.lock
log file = /var/log/rsyncd.log
ignore errors
read only = false
list = false
hosts allow = 172.16.1.0/24
# hosts deny = 0.0.0.0/32
auth users = rsync_backup
secrets file = /etc/rsync.password

[backup]
comment = "Backup directory by Will"
path = /backup

EOF

# hosts allow 与 hosts deny 只可用一个，两个同时用会都失效

# 多模块可共享配置也可单独配置
# 上方的配置项放在模块下则仅对当前模块生效
# 这几项放在模块下的时候则代表分开配置
```



### 创建用户和备份目录

```
useradd rsync

id rsync

mkdir -p /backup

chown -R rsync.rsync /backup

ls -ld /backup
```



### 密码文件配置&检查

```
echo "rsync_backup:rsync_password" > /etc/rsync.password

# 引号里边冒号左边为/etc/rsyncd.conf文件中的auth users项的值, 冒号后边为自定义的密码

chmod 600 /etc/rsync.password


cat /etc/rsync.password

ls -l /etc/rsync.password
```

###

### 启动 & 检查服务状态

```
systemctl start rsyncd
systemctl enable rsyncd
systemctl status rsyncd

# CentOS 7


rsync --daemon

# CentOS 6
```

```
ps -ef | grep rsync | grep -v grep

netstat -lntup | grep 873
```





{% hint style="success" %}
**CLIENT: nfs01 （以下配置在客户端nfs01中完成）**
{% endhint %}

### 密码文件配置&检查

```
echo "rsync_password" > /etc/rsync.password

# 引号里边为服务端中/etc/rsync.password文件中设置的密码

chmod 600 /etc/rsync.password


cat /etc/rsync.password

ls -l /etc/rsync.password


# 此为客户端的配置方法一
```

```
echo 'export RSYNC_PASSWORD=rsync_password' >> /etc/bashrc

tail -1 /etc/bashrc

. /etc/bashrc

echo $RSYNC_PASSWORD


# 此为客户端的配置方法二
```



### pull

```
rsync [OPTION...] [USER@]HOST::SRC... [DEST]

rsync [OPTION...] rsync://[USER@]HOST[:PORT]/SRC... [DEST]


e.g.:

rsync -avz rsync_backup@172.16.1.41::backup/FILE /tmp --password-file=/etc/rsync.password

# 使用用户rsync_backup把远程服务器的[backup]模块设置的目录下的FILE文件拉回本地/tmp目录
# 去掉模块后的/FILE则可把整个模块设置的文件夹拉回来
# 后边指定的密码文件为上边客户端密码文件的配置方法一配置的文件

rsync -avz rsync_backup@172.16.1.41::backup/FILE /tmp

# 若使用了客户端的密码配置方法二，则可以省略--password-file选项


rsync -avz rsync://rsync_backup@172.16.1.41/backup/FILE /tmp

# 上边命令的另一种写法（另一种语法形式）
```



### push

```
rsync [OPTION...] SRC... [USER@]HOST::DEST

rsync [OPTION...] SRC... rsync://[USER@]HOST[:PORT]/DEST


e.g.:

rsync -avz /etc rsync_backup@172.16.1.41::backup --password-file=/etc/rsync.password

# 把本地的/etc目录使用用户rsync_backup推到远程服务器的[backup]模块设置的目录下
# 后边指定的密码文件为上边客户端密码文件的配置方法一配置的文件

rsync -avz /etc rsync_backup@172.16.1.41::backup

# 若使用了客户端的密码配置方法二，则可以省略--password-file选项


rsync -avz /etc rsync://rsync_backup@172.16.1.41/backup

# 上边命令的另一种写法（另一种语法形式）


rsync -avz --exclude={1..5} /tmp/ rsync_backup@172.16.1.41::backup/FILE --password-file=/etc/rsync.password

# 排除文件1-5

rsync -avz --exclude-from=/root/paich.txt /tmp/ rsync_backup@172.16.1.41::backup/FILE --password-file=/etc/rsync.password

# 从/root/paichu.txt获取排除的文件名（paichu.txt文件名一行一个）
```

```
rsync -avz --delete /tmp/ rsync_backup@172.16.1.41::backup/FILE --password-file=/etc/rsync.password

# 本地目录有啥，有段就有啥
# 注意：远端目录是不是有更多内容，多的内容将会被删除。

# --delete会让两台服务器目录保持一致（极端情况下才用）
# --delete参数慎用
```
