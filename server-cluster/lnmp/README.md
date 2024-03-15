# LNMP

### MySQL安装（二进制方式）

#### 创建用户

```
useradd -s /sbin/nologin -M mysql
```

#### 创建目录及下载软件包

```
cd /server/tools/

wget http://mirrors.163.com/mysql/Downloads/MySQL-5.7/mysql-5.7.26-linux-glibc2.12-x86_64.tar.gz
```

#### 解压及移动

```
tar -xf mysql-5.7.26-linux-glibc2.12-x86_64.tar.gz


mkdir /application

mv mysql-5.7.26-linux-glibc2.12-x86_64 /application/mysql-5.7.26


ln -s /application/mysql-5.7.26/  /application/mysql
```

#### 配置配置文件

```
rpm -qa mariadb-libs

ls -l /etc/my.cnf

rpm -e --nodeps mariadb-libs

# 卸载mariadb-libs，不卸载依赖，卸载完后 /etc/my.cnf 将不存在
```

```
cat > /etc/my.cnf <<EOF
[mysqld]
basedir = /application/mysql/
datadir = /application/mysql/data
socket = /tmp/mysql.sock
server_id = 1
port = 3306
log_error = /application/mysql/log/mysqld.log

[mysql]
socket = /tmp/mysql.sock
prompt = will [\\d]>
EOF
```

#### 初始化数据库

```
yum install libaio-devel -y
```

```
mkdir -p /application/mysql/data

mkdir -p /application/mysql/log

chown -R mysql.mysql /application/mysql/

# 注意：经常在生产环境中会将数据目录和软件目录分离，故需要将软件和数据目录都进行修改用户
```

```
/application/mysql/bin/mysqld --initialize-insecure --user=mysql --basedir=/application/mysql/ --datadir=/application/mysql/data
```

#### 配置启动服务

```
cat > /etc/systemd/system/mysqld.service << EOF
[Unit]
Description=MySQL Server by Will
Documentation=man:mysqld(8)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target
After=syslog.target
[Install]
WantedBy=multi-user.target
[Service]
User=mysql
Group=mysql
ExecStart=/application/mysql/bin/mysqld --defaults-file=/etc/my.cnf
LimitNOFILE = 5000
EOF
```

#### 启动

```
systemctl start mysqld

systemctl enable mysqld

systemctl status mysqld


# CentOS6 启动方式如下：

cd /application/mysql/support-files/

./mysql.server start
# start stop restart status 都可执行


# sys-v 方式启动

cp /application/mysql/support-files/mysql.server /etc/init.d/mysqld

service mysqld start
```

#### 配置环境变量登录

```
echo 'export PATH=/application/mysql/bin:$PATH' >> /etc/profile

. /etc/profile

echo $PATH
```

```
mysql

> quit
```

#### 有问题可看错误日志

```
cat /application/mysql/log/mysqld.log

# 此日志在配置文件中设置 /etc/my.cnf
```

#### 修改密码

```
mysqladmin -u root password '123456'
```

#### 登录

```
mysql -u root -p
# 交互式

mysql -uroot -p123456
# 非交互式
```



### MySQL YUM安装

#### 安装清华MySQL yum源

```
cat > /etc/yum.repos.d/mysql57-community-el7-tsinghua.repo << EOF
[mysql57-community]
name=MySQL 5.7 Community Server Tsinghua
#baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/\$basearch/
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/
enabled=1
gpgcheck=1
gpgkey=http://repo.mysql.com/RPM-GPG-KEY-mysql
EOF

```

```
yum install mysql-community-server -y
```

```
# 安装后临时默认密码查看方式：
# grep "temporary password" /var/log/mysqld.log
# 可使用 mysql_secure_installation 进行安全初始化
```

> yum方式安装的MySQL配置文件如下

```
cat /etc/my.cnf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

```
ls -l /etc/systemd/system/multi-user.target.wants/mysqld.service

# lrwxrwxrwx 1 root root 38 Feb  9 12:50 /etc/systemd/system/multi-user.target.wants/mysqld.service -> /usr/lib/systemd/system/mysqld.service


ls -l | grep mysql

# -rw-r--r--  1 root root 2033 Dec 18 21:25 mysqld.service
# -rw-r--r--  1 root root 2064 Dec 18 21:25 mysqld@.service
```

```
cat /etc/systemd/system/multi-user.target.wants/mysqld.service
[Unit]
Description=MySQL Server
Documentation=man:mysqld(8)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target
After=syslog.target

[Install]
WantedBy=multi-user.target

[Service]
User=mysql
Group=mysql

Type=forking

PIDFile=/var/run/mysqld/mysqld.pid

# Disable service start and stop timeout logic of systemd for mysqld service.
TimeoutSec=0

# Execute pre and post scripts as root
PermissionsStartOnly=true

# Needed to create system tables
ExecStartPre=/usr/bin/mysqld_pre_systemd

# Start main service
ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS

# Use this to switch malloc implementation
EnvironmentFile=-/etc/sysconfig/mysql

# Sets open_files_limit
LimitNOFILE = 5000

Restart=on-failure

RestartPreventExitStatus=1

PrivateTmp=false
```



### PHP编译安装

#### 创建用户

```
useradd -s /sbin/nologin -M www

# 此用户在安装nginx时已经创建，若没有则在此处创建，用于启动php-fpm
```

#### 安装PHP调用的库

```
yum install zlib-devel libxml2-devel libjpeg-devel libjpeg-turbo-devel libiconv-devel -y

yum install freetype-devel libpng-devel gd-devel libcurl-devel libxslt-devel libxslt-devel -y

yum install libmcrypt-devel mhash mcrypt -y 
```

```
# 编译安装 libiconv

cd /server/tools/

wget http://ftp.gnu.org/gnu/libiconv/libiconv-1.16.tar.gz

tar -xf libiconv-1.16.tar.gz

cd libiconv-1.16


./configure --prefix=/application/libiconv

make && make install
```

#### 下载及编译PHP

```
cd /server/tools/

wget http://mirrors.sohu.com/php/php-7.3.5.tar.gz

tar xf php-7.3.5.tar.gz

cd php-7.3.5/


./configure \
--prefix=/application/php-7.3.5 \
--enable-mysqlnd  \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-iconv-dir=/application/libiconv \
--with-freetype-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib \
--with-libxml-dir=/usr \
--enable-xml \
--disable-rpath \
--enable-bcmath \
--enable-shmop \
--enable-sysvsem \
--enable-inline-optimization \
--with-curl \
--enable-mbregex \
--enable-fpm \
--enable-mbstring \
--with-gd \
--with-openssl \
--with-mhash \
--enable-pcntl \
--enable-sockets \
--with-xmlrpc \
--enable-soap \
--enable-short-tags \
--enable-static \
--with-xsl \
--with-fpm-user=www \
--with-fpm-group=www \
--enable-ftp \
--enable-opcache=no


# 需注意此处使用的用户，需要跟nginx一样，如果nginx是yum安装则用nginx
# 如果是编译安装时手动指定的则需与之一致

echo $?

make && make install

echo $?
```

```
ln -s /application/php-7.3.5/ /application/php

ls -l /application/php
```

#### 配置php.ini (php解析器配置文件)

```
cd /server/tools/php-7.3.5/

cp php.ini-development /application/php/lib/php.ini
```

#### 配置php fpm

```
cd /application/php/etc/

cp php-fpm.conf.default php-fpm.conf


cd php-fpm.d/

cp www.conf.default www.conf
```

#### 启动php

```
/application/php/sbin/php-fpm

netstat -ntlp | grep php-fpm
```

#### 写入开机自启动

```
vim /etc/rc.d/rc.local

/application/nginx/sbin/nginx
/application/php/sbin/php-fpm

chmod u+x /etc/rc.d/rc.local
```

#### 配置nginx转发php请求

```
server {
    listen       80;
    server_name  blog.will.com;

    root   /usr/share/nginx/html/blog;

    location / {
        index  index.php index.html index.htm;
    }


    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

}

# location中root相同可提取出来放到外面
```

```
mkdir -p /usr/share/nginx/html/blog

cd /usr/share/nginx/html/blog


echo "hello php index" > index.php

echo "<?php phpinfo(); ?>" > info.php
```

#### 访问测试php

```
使用浏览器访问：

blog.will.com
blog.will.com/info.php
```

#### 测试php连接MySQL

```
vim /usr/share/nginx/html/blog/test_mysql.php


<?php
//$link_id=mysqli_connect('主机名','用户','密码');
        $link_id=mysqli_connect('localhost','root','123456') or mysql_error();
        if($link_id){
                echo "mysql successful by Will.\n";
        }else{
                echo mysql_error();
        }
?>
```

```
# 使用浏览器访问

http://blog.will.com/test_mysql.php
```
