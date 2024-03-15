# Zabbix 安装

### Home Page :

{% embed url="https://www.zabbix.com/" %}



### Install and configure Zabbix server for your platform

#### a. Install Zabbix repository

```
rpm -ivh https://mirrors.aliyun.com/zabbix/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-2.el7.noarch.rpm

# 使用阿里云yum源安装 Zabbix 4.0


vim /etc/yum.repos.d/zabbix.repo

# 修改官网baseurl为阿里云baseurl

[zabbix]
name=Zabbix Official Repository - $basearch
#baseurl=http://repo.zabbix.com/zabbix/4.0/rhel/7/$basearch/
baseurl=https://mirrors.aliyun.com/zabbix/zabbix/4.0/rhel/7/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-ZABBIX-A14FE591

[zabbix-debuginfo]
name=Zabbix Official Repository debuginfo - $basearch
#baseurl=http://repo.zabbix.com/zabbix/4.0/rhel/7/$basearch/debuginfo/
baseurl=https://mirrors.aliyun.com/zabbix/zabbix/4.0/rhel/7/$basearch/debuginfo/
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-ZABBIX-A14FE591
gpgcheck=1

[zabbix-non-supported]
name=Zabbix Official Repository non-supported - $basearch
#baseurl=http://repo.zabbix.com/non-supported/rhel/7/$basearch/
baseurl=https://mirrors.aliyun.com/zabbix/non-supported/rhel/7/$basearch/
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-ZABBIX
gpgcheck=1


yum clean all

yum makecache
```

```
yum install httpd -y


systemctl start httpd

systemctl enable httpd


firewall-cmd --permanent --add-service=http

firewall-cmd --reload
```

```
yum install mariadb mariadb-server -y


systemctl start mariadb

systemctl enable mariadb


mysql_secure_installation


firewall-cmd --permanent --add-service=mysql

firewall-cmd --reload
```

#### b. Install Zabbix server, frontend, agent

```
yum install zabbix-server-mysql zabbix-web-mysql -y
```

#### c. Create initial database

```
mysql -uroot -p

password

> create database zabbix character set utf8 collate utf8_bin;

> grant all privileges on zabbix.* to zabbix@localhost identified by 'password';

> quit;
```

Import initial schema and data. You will be prompted to enter your newly created password.

```
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix
```

#### d. Configure the database for Zabbix server

```
vim /etc/zabbix/zabbix_server.conf

DBPassword=password

# your zabbix database password
```

#### e. Configure PHP for Zabbix frontend

```
vim /etc/httpd/conf.d/zabbix.conf

php_value date.timezone Asia/Shanghai
```

#### f. Configure SELinux

```
setsebool -P httpd_can_connect_zabbix on

# If the database is accessible over network (including 'localhost' in case of PostgreSQL), you need to allow Zabbix frontend to connect to the database too:

setsebool -P httpd_can_network_connect_db on


ausearch -c 'zabbix_server' --raw | audit2allow -M my-zabbixserver

semodule -i my-zabbixserver.pp
```

#### g.Start Zabbix server and agent processes

```
systemctl restart zabbix-server

systemctl enable zabbix-server
```

#### h. Configure Zabbix frontend

```
http://server_ip_or_name/zabbix
```



### Install Zabbix agent

```
# yum install zabbix-agent -y


rpm -ivh https://mirrors.aliyun.com/zabbix/zabbix/4.0/rhel/7/x86_64/zabbix-agent-4.0.15-1.el7.x86_64.rpm

#
# 遗留问题，稍后解决

firewall-cmd --permanent --add-port=10050/tcp
firewall-cmd --reload


systemctl start zabbix-agent

systemctl enable zabbix-agent


vim /etc/zabbix/zabbix_agentd.conf
Server=10.0.0.61
# 不同机器需要修改
```



{% hint style="warning" %}
查看日志：tail -f /var/log/messages
{% endhint %}





### onekey\_install\_zabbix.sh

```bash
#!/bin/bash

. /etc/init.d/functions


# Configure yum repository

# add zabbix yum
rpm -ivh https://mirrors.aliyun.com/zabbix/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-2.el7.noarch.rpm
sed -i 's#http://repo.zabbix.com/#https://mirrors.aliyun.com/zabbix/#g' /etc/yum.repos.d/zabbix.repo

# add mysql5.7 yum
cat > /etc/yum.repos.d/mysql57-community-el7-tsinghua.repo << EOF
[mysql57-community]
name=MySQL 5.7 Community Server Tsinghua
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/
enabled=1
gpgcheck=1
gpgkey=http://repo.mysql.com/RPM-GPG-KEY-mysql
EOF

# rebuild yum 
yum clean all
yum makecache



# install httpd
yum install httpd -y

systemctl start httpd
systemctl enable httpd

firewall-cmd --permanent --add-service=http
firewall-cmd --reload



# install mysqld
yum install mysql-community-server -y

systemctl start mysqld
systemctl enable mysqld

temporary_password=`grep "temporary password" /var/log/mysqld.log | awk '{print $NF}'`


rpm -qa | grep expect > /dev/null 2>&1

if [ $? -ne 0 ]; then
    yum install expect -y > /dev/null 2>&1
fi

/usr/bin/expect << EOF
spawn mysql_secure_installation

expect "Enter password for user root:"
send "${temporary_password}\r"

expect "New password:"
send "NewPass0.0\r"

expect "Re-enter new password:"
send "NewPass0.0\r"

expect "Change the password for root ? ((Press y|Y for Yes, any other key for No) :"
send "n\r"

expect "Remove anonymous users? (Press y|Y for Yes, any other key for No) :"
send "y\r"

expect "Disallow root login remotely? (Press y|Y for Yes, any other key for No) :"
send "y\r"

expect "Remove test database and access to it? (Press y|Y for Yes, any other key for No) :"
send "y\r"

expect "Reload privilege tables now? (Press y|Y for Yes, any other key for No) :"
send "y\r"

expect eof

EOF

firewall-cmd --permanent --add-service=mysql
firewall-cmd --reload



# install Zabbix
yum install zabbix-server-mysql zabbix-web-mysql -y



# 创建及配置Zabbix MySQL数据库
mysql -u root -pNewPass0.0 -e "create database zabbix character set utf8 collate utf8_bin;"
mysql -u root -pNewPass0.0 -e "grant all privileges on zabbix.* to zabbix@localhost identified by 'NewPass0.0';"

zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -pNewPass0.0 zabbix

# 修改zabbix配置文件
sed -i 's/# DBHost=localhost/DBHost=localhost/g' /etc/zabbix/zabbix_server.conf
sed -i 's/# DBPassword=/DBPassword=NewPass0.0/g' /etc/zabbix/zabbix_server.conf

# 配置httpd zabbix时区
sed -i 's#\# php_value date.timezone Europe/Riga#php_value date.timezone Asia/Shanghai#g' /etc/httpd/conf.d/zabbix.conf



# Configure SELinux
setsebool -P httpd_can_connect_zabbix on

# If the database is accessible over network (including 'localhost' in case of PostgreSQL), you need to allow Zabbix frontend to connect to the database too:
setsebool -P httpd_can_network_connect_db on


systemctl restart httpd
systemctl start zabbix-server
systemctl enable zabbix-server

ausearch -c 'zabbix_server* denied' --raw | audit2allow -M my-zabbixserver
# grep zabbix-server /var/log/audit/audit.log | audit2allow -M my-zabbixserver
semodule -i my-zabbixserver.pp


# 重启服务
systemctl restart zabbix-server
```



### Docker安装Zabbix

#### 1.Create network dedicated for Zabbix component containers:

```bash
docker network create --subnet 172.20.0.0/16 --ip-range 172.20.240.0/20 zabbix-net
```

#### 2.Start empty MySQL server instance

```bash
docker run --name mysql-server -t \
    -e MYSQL_DATABASE="zabbix" \
    -e MYSQL_USER="zabbix" \
    -e MYSQL_PASSWORD="zabbix_pwd" \
    -e MYSQL_ROOT_PASSWORD="root_pwd" \
    --network=zabbix-net \
    --restart unless-stopped \
    -d mysql:5.7 \
    --character-set-server=utf8 --collation-server=utf8_bin
```

#### 3.Start Zabbix Java gateway instance

```bash
docker run --name zabbix-java-gateway -t \
    --network=zabbix-net \
    --restart unless-stopped \
    -d zabbix/zabbix-java-gateway:4.0-centos-latest
```

#### 4.Start Zabbix server instance and link the instance with created MySQL server instance

```bash
docker run --name zabbix-server-mysql -t \
    -e DB_SERVER_HOST="mysql-server" \
    -e MYSQL_DATABASE="zabbix" \
    -e MYSQL_USER="zabbix" \
    -e MYSQL_PASSWORD="zabbix_pwd" \
    -e MYSQL_ROOT_PASSWORD="root_pwd" \
    -e ZBX_JAVAGATEWAY="zabbix-java-gateway" \
    --network=zabbix-net \
    -p 10051:10051 \
    --restart unless-stopped \
    -d zabbix/zabbix-server-mysql:4.0-centos-latest
```

{% hint style="info" %}
Zabbix server instance exposes 10051/TCP port (Zabbix trapper) to host machine.
{% endhint %}

#### 5.Start Zabbix web interface and link the instance with created MySQL server and Zabbix server instances

```bash
docker run --name zabbix-web-nginx-mysql -t \
    -e ZBX_SERVER_HOST="zabbix-server-mysql" \
    -e DB_SERVER_HOST="mysql-server" \
    -e MYSQL_DATABASE="zabbix" \
    -e MYSQL_USER="zabbix" \
    -e MYSQL_PASSWORD="zabbix_pwd" \
    -e MYSQL_ROOT_PASSWORD="root_pwd" \
    -e PHP_TZ="Asia/Shanghai" \
    --network=zabbix-net \
    -p 80:8080 \
    --restart unless-stopped \
    -d zabbix/zabbix-web-nginx-mysql:4.0-centos-latest
```

{% hint style="info" %}
Zabbix web interface instance exposes 80/TCP port (HTTP) to host machine.
{% endhint %}
