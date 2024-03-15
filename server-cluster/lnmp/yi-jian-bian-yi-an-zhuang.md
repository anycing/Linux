# 一键编译安装

## onekey\_install\_lnmp.sh

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


# 配置及分发hosts文件
cat > /etc/hosts << EOF
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
172.16.1.15  lb01
172.16.1.16  lb02
172.16.1.17  web01
172.16.1.18  web02
172.16.1.31  nfs01
172.16.1.41  backup
172.16.1.51  db01
EOF

ansible all --private-key ~/.ssh/id_cluster -m copy -a "src=/etc/hosts dest=/etc/hosts"


# --- 分发软件包 --- #

# 分发nginx软件包
ansible lb_server,web_server --private-key ~/.ssh/id_cluster -m copy -a "src=/server/tools/nginx-1.16.0.tar.gz dest=/server/tools/"


# 分发编译php所依赖软件包
ansible web_server --private-key ~/.ssh/id_cluster -m copy -a "src=/server/tools/libiconv-1.16.tar.gz dest=/server/tools/"


# 分发php源代码
ansible web_server --private-key ~/.ssh/id_cluster -m copy -a "src=/server/tools/php-7.3.5.tar.gz dest=/server/tools/"


# 分发mysql软件
ansible db_server --private-key ~/.ssh/id_cluster -m copy -a "src=/server/tools/mysql-5.7.26-linux-glibc2.12-x86_64.tar.gz dest=/server/tools/"


# 分发网站程序源码
ansible web_server --private-key ~/.ssh/id_cluster -m copy -a "src=/server/tools/wordpress-5.3.2.tar.gz dest=/server/tools/"



# --- 分发脚本 --- #

# 分发nginx安装脚本
ansible lb_server,web_server --private-key ~/.ssh/id_cluster -m copy -a "src=/server/scripts/onekey_install_nginx_compile.sh dest=/server/scripts/"

# 分发php安装脚本
ansible web_server --private-key ~/.ssh/id_cluster -m copy -a "src=/server/scripts/onekey_install_php_compile.sh dest=/server/scripts/"

# 分发MySQL安装脚本
ansible db_server --private-key ~/.ssh/id_cluster -m copy -a "src=/server/scripts/onekey_install_mysql_binary.sh dest=/server/scripts/"
ansible db_server --private-key ~/.ssh/id_cluster -m copy -a "src=/server/scripts/chpwd_mysql.sh dest=/server/scripts/"



# --- 执行安装 --- #

# 安装Nginx
ansible lb_server,web_server --private-key ~/.ssh/id_cluster -m script -a "/server/scripts/onekey_install_nginx_compile.sh"

# 安装PHP
ansible web_server --private-key ~/.ssh/id_cluster -m script -a "/server/scripts/onekey_install_php_compile.sh"

# 安装MySQL
ansible db_server --private-key ~/.ssh/id_cluster -m script -a "/server/scripts/onekey_install_mysql_binary.sh"
ansible db_server --private-key ~/.ssh/id_cluster -m script -a "/server/scripts/chpwd_mysql.sh"


```

### distribute\_keys.sh

```
#!/bin/bash

yum install sshpass -y > /dev/null

ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_cluster -q

for ip in $(cat $1)
do
    sshpass -p "centos7" ssh-copy-id -i ~/.ssh/id_cluster.pub -o StrictHostKeyChecking=no root@$ip
done
```

### ip\_cluster

```
172.16.1.15
172.16.1.16
172.16.1.17
172.16.1.18
172.16.1.31
172.16.1.41
172.16.1.51
```

### ansible\_server\_install.sh

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

### onekey\_install\_nginx\_compile.sh

```
# install software
yum install gcc make pcre-devel openssl-devel -y > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "install software succeed"
else
    echo "install software fail"
fi


# download and compile nginx
mkdir -p /server/tools
mkdir -p /application
cd /server/tools

#wget http://nginx.org/download/nginx-1.16.0.tar.gz > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "download nginx succeed"
else
    echo "download nginx fail"
fi

tar xzf nginx-1.16.0.tar.gz

cd nginx-1.16.0/

useradd -u 1111 -s /sbin/nologin -M www


./configure  --user=www --group=www --prefix=/application/nginx-1.16.0/ --with-http_stub_status_module  --with-http_ssl_module --with-pcre > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "configure nginx succeed"
else
    echo "connfigure nginx fail"
fi

make -j4 > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "compile nginx succeed"
else
    echo "compile nginx fail"
fi

make install > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "install nginx succeed"
else
    echo "install nginx fail"
fi

# create soft link
ln -s /application/nginx-1.16.0/ /application/nginx


# start nginx
/application/nginx/sbin/nginx

if [ $? -eq 0 ]; then
    echo "start nginx succeed"
else
    echo "start nginx fail"
fi


# configure firewall
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --reload

if [ $? -eq 0 ]; then
    echo "configure firewall succeed"
else
    echo "configure firewall fail"
fi


# write to startup
echo '/application/nginx/sbin/nginx' >> /etc/rc.d/rc.local

chmod u+x /etc/rc.d/rc.local

if [ $? -eq 0 ]; then
    echo "write nginx to startup succeed"
else
    echo "write nginx to startup fail"
fi


# configure nginx configuration
cp /application/nginx/conf/nginx.conf{,.backup}

mkdir -p /application/nginx/conf/extra

egrep -v "#|^$" /application/nginx/conf/nginx.conf.backup > /application/nginx/conf/nginx.conf
# 日志需要手动配置，否则无效
sed -i "22 i \ \ \ \ include extra/*.conf;" /application/nginx/conf/nginx.conf

if [ $? -eq 0 ]; then
    echo "configure nginx configuration succeed"
else
    echo "configure nginx configuration fail"
fi
```

### onekey\_install\_php\_compile.sh

```
#!/bin/bash

# install php callback software
yum install zlib-devel libxml2-devel libjpeg-devel libjpeg-turbo-devel libiconv-devel -y > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "install php callback software1 succeed"
else
    echo "install php callback software1 fail"
fi


yum install freetype-devel libpng-devel gd-devel libcurl-devel libxslt-devel libxslt-devel -y > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "install php callback software2 succeed"
else
    echo "install php callback software2 fail"
fi


yum install libmcrypt-devel mhash mcrypt -y > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "install php callback software3 succeed"
else
    echo "install php callback software3 fail"
fi


# install libiconv
cd /server/tools/
mkdir -p /application

#wget http://ftp.gnu.org/gnu/libiconv/libiconv-1.16.tar.gz

tar -xzf libiconv-1.16.tar.gz

cd libiconv-1.16


./configure --prefix=/application/libiconv > /dev/null 2>&1

make -j4 > /dev/null 2>&1

make install > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "install libiconv succeed"
else
    echo "install libiconv fail"
fi


# configure and compile install php
cd /server/tools/

#wget http://mirrors.sohu.com/php/php-7.3.5.tar.gz

tar xzf php-7.3.5.tar.gz

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
--enable-opcache=no > /dev/null 2>&1

# 需注意此处使用的用户，需要跟nginx一样，如果nginx是yum安装则用nginx
# 如果是编译安装时手动指定的则需与之一致

make -j4 > /dev/null 2>&1
make install > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "configure and compile install php succeed"
else
    echo "configure and compile install php fail"
fi


ln -s /application/php-7.3.5/ /application/php > /dev/null 2>&1


# configure php.ini
cd /server/tools/php-7.3.5/

cp php.ini-development /application/php/lib/php.ini

if [ $? -eq 0 ]; then
    echo "configure php.ini succeed"
else
    echo "configure php.ini fail"
fi


# configure php fpm
cd /application/php/etc/

cp php-fpm.conf.default php-fpm.conf

cd php-fpm.d/

cp www.conf.default www.conf

if [ $? -eq 0 ]; then
    echo "configure php fpm succeed"
else
    echo "configure php fpm fail"
fi


# start php
/application/php/sbin/php-fpm

if [ $? -eq 0 ]; then
    echo "start php succeed"
else
    echo "start php fail"
fi


# write php to startup
echo '/application/php/sbin/php-fpm' >> /etc/rc.d/rc.local

chmod u+x /etc/rc.d/rc.local

if [ $? -eq 0 ]; then
    echo "write php to startup succeed"
else
    echo "write php to startup fail"
fi


# configure nginx support php
cat > /application/nginx/conf/extra/www.conf <<EOF
server {
    listen       80;
    server_name  www.will.com;

    root   /usr/share/nginx/html/www;

    location / {
        index  index.php index.html index.htm;
    }

    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME \$document_root\$fastcgi_script_name;
        include        fastcgi_params;
    }
}
EOF

if [ $? -eq 0 ]; then
    echo "configure nginx support php succeed"
else
    echo "configure nginx support php fail"
fi


# create website test code
mkdir -p /usr/share/nginx/html/www
chown -R www.www /usr/share/nginx/html/www

cd /usr/share/nginx/html/www

echo "hello php index" > index.php

echo "<?php phpinfo(); ?>" > info.php

if [ $? -eq 0 ]; then
    echo "create website test code succeed"
else
    echo "create website test code fail"
fi


# reload nginx
/application/nginx/sbin/nginx -s reload

if [ $? -eq 0 ]; then
    echo "reload nginx succeed"
else
    echo "reload nginx fail"
fi

```

### onekey\_install\_mysql\_binary.sh

```
# add user 
useradd -s /sbin/nologin -M mysql > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "useradd succeed"
else
    echo "useradd fail"
    exit
fi


# download MySQL
cd /server/tools/
mkdir -p /application

#wget http://mirrors.163.com/mysql/Downloads/MySQL-5.7/mysql-5.7.26-linux-glibc2.12-x86_64.tar.gz > /dev/null 2>&1

if [ $? -eq 0 ] && [ -f mysql-*.tar.gz ]; then
    echo "download MySQL succeed"
else
    echo "download MySQL fail"
    exit
fi


# decompress

tar -xzf mysql-5.7.26-linux-glibc2.12-x86_64.tar.gz

mv mysql-5.7.26-linux-glibc2.12-x86_64 /application/mysql-5.7.26


ln -s /application/mysql-5.7.26/  /application/mysql


rpm -e --nodeps mariadb-libs

# configure my.cnf
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
EOF

if [ $? -eq 0 ]; then
    echo "configure my.cnf succeed"
else
    echo "configure my.cnf fail"
    exit
fi


yum install libaio-devel -y > /dev/null 2>&1


mkdir -p /application/mysql/data

mkdir -p /application/mysql/log

chown -R mysql.mysql /application/mysql/


# initial
/application/mysql/bin/mysqld --initialize-insecure --user=mysql --basedir=/application/mysql/ --datadir=/application/mysql/data

if [ $? -eq 0 ]; then
    echo "initial MySQL succeed"
else
    echo "initial MySQL fail"
    exit
fi


# write service
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

if [ $? -eq 0 ]; then
    echo "write service succeed"
else
    echo "write service fail"
    exit
fi


# start mysqld
systemctl start mysqld
systemctl enable mysqld

if [ $? -eq 0 ]; then
    echo "start MySQL succeed"
else
    echo "start MySQL fail"
    exit
fi


# 环境变量需退出后执行才生效
# add env
echo 'export PATH=/application/mysql/bin:$PATH' >> /etc/profile
source /etc/profile

if [ $? -eq 0 ]; then
    echo "add env succeed"
else
    echo "add env fail"
fi

```

### chpwd\_mysql.sh

```
# change mysql password
/application/mysql/bin/mysqladmin -u root password '123456' > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "change MySQL password succeed"
else
    echo "change MySQL password fail"
fi

# 如果直接使用mysqladmin，最后一步在脚本中无法成功，因为环境变量的原因

# mysql添加用户
# grant all on wordpress.* to admin@'172.16.1.%' identified by 'admin';
# flush privileges;
```
