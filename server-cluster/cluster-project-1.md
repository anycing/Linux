# Cluster Project  1

## 全网备份实战

#### 项目实施可用服务器列表

| hostname | ip          |
| -------- | ----------- |
| web01    | 172.16.1.17 |
| web02    | 172.16.1.18 |
| nfs01    | 172.16.1.31 |
| backup   | 172.16.1.41 |
| m01      | 172.16.1.61 |



{% hint style="success" %}
**集群服务器全网数据备份解决方案**
{% endhint %}

### 项目要求一：全网数据备份

> 每天晚上 00 点整在 web 服务器（web01、web02）上打包备份系统配置文件、网站程序目录及访问日志，并通过 rsync  命令推送至备份服务器（backup）上保存

#### 详细要求

> 1\. web  服务器和 backup 备份服务器的备份目录都必须为 /backup
>
> 2\. 要求备份的系统配置文件包含但不限于：
>
> &#x20;   a. 定时任务服务的配置文件 ( /var/spool/cron/root )
>
> &#x20;   b. 开机自启动的配置文件  ( /etc/rc.d/rc.local )
>
> &#x20;   c. 日常脚本的目录 ( /server/scripts )
>
> 3\. Web服务器目录 ( /var/www/html )
>
> 4\. Web服务器日志 ( /app/logs )
>
> 5\. Web服务器本地保留打包后7天的备份数据即可
>
> 6\. 备份服务器 backup 上保留最近7天的备份数据，同时保留6个月内每周一的所有数据副本
>
> 7\. 备份服务器 backup 上按照备份数据服务器上的内网IP为目录保存备份，备份的文件按照时间名字保存
>
> 8\. 需要确保数据完整正确，每天早上8:00 把备份成功或失败的结果信息发送至系统管理员邮箱



{% hint style="success" %}
**网站集群后端 NFS 共享存储搭建及优化解决方案**
{% endhint %}

### 项目要求二：后端共享存储

> 1\. 在 NFS 服务端 (nfs01) 上共享 /data/w\_shared 及 /data/r\_shared 两个文件目录，允许从 NFS 客户端 web01、web02 上分别挂载共享目录后可实现从 web01、web02 上只读 /data/r\_shared ，可写 /data/w\_shared
>
> 2\. NFS 客户端 web01、web02 上的挂载点为 /data/b\_w （写）， /data/b\_r （读），
>
> 3\. 从 NFS 客户端 web02 上的可写挂载点目录创建任意文件，从 NFS 客户端 web01 上可以删除这个创建的文件，反之也可以

#### 问答题：如何优化 NFS 服务，大并发场景 NFS 压力大如何解决？



{% hint style="success" %}
**网站集群后端 NFS 共享存储单点实时数据同步解决方案**
{% endhint %}

### 项目要求三：NFS 共享存储实时数据同步

> 当用户通过 web 服务器将数据写入到 NFS 服务器 nfs01 时，同时复制到备份服务器 backup 上。



### 项目实现总架构逻辑图



### 一键部署脚本

> **所有脚本存放于管理机 m01 的 /server/scripts 目录下**

| shell                        | 说明                                       |
| ---------------------------- | ---------------------------------------- |
| onekey\_install\_cluster.sh  | 一键安装总脚本                                  |
| distribute\_keys.sh          | onekey distribute keys                   |
| ansible\_server\_install.sh  | install ansible on management server     |
| rsync\_server\_install.sh    | install rsync server                     |
| rsync\_client\_install.sh    | install rsync client                     |
| nfs\_server\_install.sh      | install nfs server                       |
| nfs\_client\_install.sh      | install nfs client                       |
| sersync\_install.sh          | install sersync on nfs server            |
| backup\_web\_server\_data.sh | backup web server data                   |
| check\_backup\_data.sh       | check backup data on backup server       |
| config\_mail.sh              | configure email service on backup server |

#### onekey\_install\_cluster.sh

```
#!/bin/bash

# onekey distribute keys
/bin/bash /server/scripts/distribute_keys.sh /server/scripts/ip_cluster

# install ansible on management server
/bin/bash /server/scripts/ansible_server_install.sh > /dev/null 2>&1
if [ $? -eq 0 ]; then
    echo "ansible_server_install succeed"
else
    echo "ansible_server_install fail"
fi



# --------- Start install server ---------

# onekey install rsync server
ansible backup_server --private-key ~/.ssh/id_cluster -m script -a "/server/scripts/rsync_server_install.sh"
if [ $? -eq 0 ]; then
    echo "onekey install rsync server succeed"
else
    echo "onekey install rsync server fail"
fi

# onekey install rsync client
ansible web_server,nfs_server --private-key ~/.ssh/id_cluster -m script -a "/server/scripts/rsync_client_install.sh"
if [ $? -eq 0 ]; then
    echo "onekey install rsync client succeed"
else
    echo "onekey install rsync client fail"
fi

# onekey install nfs server
ansible nfs_server --private-key ~/.ssh/id_cluster -m script -a "/server/scripts/nfs_server_install.sh"
if [ $? -eq 0 ]; then
    echo "onekey install nfs server succeed"
else
    echo "onekey install nfs server fail"
fi

# onekey install nfs client
ansible web_server --private-key ~/.ssh/id_cluster -m script -a "/server/scripts/nfs_client_install.sh"
if [ $? -eq 0 ]; then
    echo "onekey install nfs client succeed"
else
    echo "onekey install nfs client fail"
fi

# onekey install sersync on nfs server
ansible nfs_server --private-key ~/.ssh/id_cluster -m script -a "/server/scripts/sersync_install.sh"
if [ $? -eq 0 ]; then
    echo "onekey install sersync on nfs server succeed"
else
    echo "onekey install sersync on nfs server fail"
fi

# --------- End install server ---------



# --------- Start backup ---------

# distribute backup shell to web server
ansible web_server --private-key ~/.ssh/id_cluster -m copy -a "src=/server/scripts/backup_web_server_data.sh dest=/server/scripts/backup_web_server_data.sh mode=0744"
if [ $? -eq 0 ]; then
    echo "distribute backup shell to web server succeed"
else
    echo "distribute backup shell to web server fail"
fi

# add backup crontab on web server
ansible web_server --private-key ~/.ssh/id_cluster -m cron -a "name='backup data' minute=0 hour=0 job='/server/scripts/backup_web_server_data.sh > /dev/null 2>&1'"
if [ $? -eq 0 ]; then
    echo "add backup crontab on web server succeed"
else
    echo "add backup crontab on web server fail"
fi

# distribute check backup data shell to backup server
ansible backup_server --private-key ~/.ssh/id_cluster -m copy -a "src=/server/scripts/check_backup_data.sh dest=/server/scripts/check_backup_data.sh mode=0744"
ansible backup_server --private-key ~/.ssh/id_cluster -m script -a "/server/scripts/config_mail.sh"
if [ $? -eq 0 ]; then
    echo "distribute check backup data shell to backup server succeed"
else
    echo "distribute check backup data shell to backup server fail"
fi

# add check data crontab on backup server
ansible backup_server --private-key ~/.ssh/id_cluster -m cron -a "name='check data' minute=0 hour=8 job='/server/scripts/check_backup_data.sh > /dev/null 2>&1'"
if [ $? -eq 0 ]; then
    echo "add check data crontab on backup server succeed"
else
    echo "add check data crontab on backup server fail"
fi

# --------- End backup ---------

```

#### distribute\_keys.sh

```
#!/bin/bash

yum install sshpass -y > /dev/null

ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_cluster -q

for ip in $(cat $1)
do
    sshpass -p "centos7" ssh-copy-id -i ~/.ssh/id_cluster.pub -o StrictHostKeyChecking=no root@$ip
done

```

#### ansible\_server\_install.sh

```
#!/bin/bash

# install ansible
yum install ansible -y

# back up hosts configuration file
cp /etc/ansible/hosts{,.backup.ori}

# configure hosts
cat > /etc/ansible/hosts << EOF
[lb_server]
172.16.1.15
172.16.1.16

[web_server]
172.16.1.17
172.16.1.18

[nfs_server]
172.16.1.31

[backup_server]
172.16.1.41

[db_server]
172.16.1.51

EOF

# back up main configuration file
cp /etc/ansible/ansible.cfg{,.backup.ori}

# configure main configuration
sed -i 's/#host_key_checking = False/host_key_checking = False/g' /etc/ansible/ansible.cfg

```

#### rsync\_server\_install.sh

```
#!/bin/bash

# install rsync
yum install rsync -y

# back up configuration file
cp /etc/rsyncd.conf{,.ori_backup}

# configure
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

# create user and directory
useradd rsync
mkdir -p /backup
chown -R rsync.rsync /backup

# create auth password file
echo "rsync_backup:rsync_password" > /etc/rsync.password
chmod 600 /etc/rsync.password

# start service and check status
systemctl start rsyncd
systemctl enable rsyncd
systemctl status rsyncd

```

#### rsync\_client\_install.sh

```
#!/bin/bash

# configure auth password file
echo "rsync_password" > /etc/rsync.password
chmod 600 /etc/rsync.password

```

#### nfs\_server\_install.sh

```
#!/bin/bash

# install nfs
yum install nfs-utils rpcbind -y  > /dev/null 2>&1

# start service
systemctl start rpcbind
systemctl start nfs

# add to startup
systemctl enable rpcbind
systemctl enable nfs

# check status
# rpcinfo -p 127.0.0.1
# netstat -ntlpu | egrep "rpc|nfs"

# configure
cat > /etc/exports << EOF
/data/w_shared 172.16.1.0/24(rw,sync)
/data/r_shared 172.16.1.0/24(ro)
EOF

# make directory
mkdir -p /data/w_shared
mkdir -p /data/r_shared
chown -R nfsnobody.nfsnobody /data/w_shared
chown -R nfsnobody.nfsnobody /data/r_shared

# reload nfs
systemctl reload nfs

```

#### nfs\_client\_install.sh

```
#!/bin/bash

# create mount dir
mkdir -p /data/b_w
mkdir -p /data/b_r

# mount right now
mount -t nfs 172.16.1.31:/data/w_shared /data/b_w
mount -t nfs 172.16.1.31:/data/r_shared /data/b_r

# startup auto mount
echo "172.16.1.31:/data/w_shared       /data/b_w               nfs     defaults,_netdev        0 0" >> /etc/fstab
echo "172.16.1.31:/data/r_shared       /data/b_r               nfs     defaults,_netdev        0 0" >> /etc/fstab
systemctl restart remote-fs.target
systemctl enable remote-fs.target

```

#### sersync\_install.sh

```
#!/bin/bash

# make directory
mkdir -p /server/tools

# download sersync
wget -O /server/tools/sersync.tar.gz https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/sersync/sersync2.5.4_64bit_binary_stable_final.tar.gz > /dev/null 2>&1

# decompress download file
tar xf /server/tools/sersync.tar.gz -C /server/tools/

# move sersync to the right path
mkdir -p /application/sersync
mkdir -p /application/sersync/bin
mkdir -p /application/sersync/conf
mkdir -p /application/sersync/logs
cp /server/tools/GNU-Linux-x86/sersync2 /application/sersync/bin
cp /server/tools/GNU-Linux-x86/confxml.xml /application/sersync/conf/confxml.xml.backup.ori

# configure sersync
cat > /application/sersync/conf/confxml.xml << EOF
<?xml version="1.0" encoding="ISO-8859-1"?>
<head version="2.5">
# 主机
    <host hostip="localhost" port="8008"></host>
    <debug start="false"/>
    <fileSystem xfs="false"/>
    
# 过滤
    <filter start="false">
        <exclude expression="(.*)\.svn"></exclude>
        <exclude expression="(.*)\.gz"></exclude>
        <exclude expression="^info/*"></exclude>
        <exclude expression="^static/*"></exclude>
    </filter>
    
# inotify 监控的事件
    <inotify>
        <delete start="true"/>
        <createFolder start="true"/>
        <createFile start="false"/>
        <closeWrite start="true"/>
        <moveFrom start="true"/>
        <moveTo start="true"/>
        <attrib start="false"/>
        <modify start="false"/>
    </inotify>

    <sersync>
# 要监控的目录
        <localpath watch="/data">
# rsync 服务器的配置参数（远程 ip 与 rsync 模块名称）
            <remote ip="172.16.1.41" name="backup"/>
        </localpath>
        <rsync>
# rsync 传输时的参数
            <commonParams params="-artuz"/>
            # 开启验证，start参数改为true
            <auth start="true" users="rsync_backup" passwordfile="/etc/rsync.password"/>
            <userDefinedPort start="false" port="874"/><!-- port=874 -->
            <timeout start="false" time="100"/><!-- timeout=100 -->
            <ssh start="false"/>
        </rsync>
        <failLog path="/tmp/rsync_fail_log.sh" timeToExecute="60"/><!--default every 60mins execute once-->
        <crontab start="false" schedule="600"><!--600mins-->
            <crontabfilter start="false">
                <exclude expression="*.php"></exclude>
                <exclude expression="info/*"></exclude>
            </crontabfilter>
        </crontab>
        <plugin start="false" name="command"/>
    </sersync>

    <plugin name="command">
        <param prefix="/bin/sh" suffix="" ignoreError="true"/>  <!--prefix /opt/tongbu/mmm.sh suffix-->
        <filter start="false">
            <include expression="(.*)\.php"/>
            <include expression="(.*)\.sh"/>
        </filter>
    </plugin>

    <plugin name="socket">
        <localpath watch="/opt/tongbu">
            <deshost ip="192.168.138.20" port="8009"/>
        </localpath>
    </plugin>
    <plugin name="refreshCDN">
        <localpath watch="/data0/htdocs/cms.xoyo.com/site/">
            <cdninfo domainname="ccms.chinacache.com" port="80" username="xxxx" passwd="xxxx"/>
            <sendurl base="http://pic.xoyo.com/cms"/>
            <regexurl regex="false" match="cms.xoyo.com/site([/a-zA-Z0-9]*).xoyo.com/images"/>
        </localpath>
    </plugin>
</head>
EOF

# start sersync service
/application/sersync/bin/sersync2 -d -o /application/sersync/conf/confxml.xml

# startup
echo "/application/sersync/bin/sersync2 -d -o /application/sersync/conf/confxml.xml" >> /etc/rc.d/rc.local
chmod u+x /etc/rc.d/rc.local


```

#### backup\_web\_server\_data.sh

```
#!/bin/bash

export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin"

IP=$(hostname -i)
HostName=$(hostname)
mkdir -p /backup/$IP

# tar打包
tar -zchf /backup/${IP}/${HostName}_$(date "+%F_%w").tar.gz /var/spool/cron/root /etc/rc.d/rc.local /server/scripts /var/www/html /app/logs &> /dev/null &&\

touch /backup/${IP}/${HostName}_$(date "+%F_%w").flag &&\

# md5采集
find /backup/${IP}/${HostName}_$(date "+%F_%w").tar.gz | xargs md5sum > /backup/${IP}/${HostName}_$(date "+%F_%w").flag &&\

# delete
find /backup/${IP}/ -type f -name "*.tar.gz" -mtime "+7" | xargs rm -f &&\

# push
rsync -az /backup/ rsync_backup@172.16.1.41::backup --password-file=/etc/rsync.password &> /dev/null

```

#### check\_backup\_data.sh

```
#!/bin/bash

export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin"

# delete
find /backup/ -type f -name "*.tar.gz" -mtime "+180" | xargs rm -f &&\

find /backup/ -type f ! -name "*_1.tar.gz" -mtime "+7" | xargs rm -f

# check md5
find /backup/ -type f -name "*$(date "+%F_%w").flag" | xargs md5sum -c > /tmp/backup_message.log

# send email
mail -s "Backup Message $(date "+%F")" anycing@qq.com < /tmp/backup_message.log

```

#### config\_mail.sh

```
#!/bin/bash

# configure mail service
echo "set from=anycing@qq.com smtp=smtp.qq.com smtp-auth-user=anycing smtp-auth-password=nxubuhuqwgcwbgh smtp-auth=login" >> /etc/mail.rc

# start mail service
systemctl restart postfix
systemctl enable postfix

```
