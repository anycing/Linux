# YUM仓库搭建

### 创建本地yum仓库

#### 创建本地yum仓库目录

```
mkdir /yum_local -p
mkdir /yum_local/nginx
mkdir /yum_local/mysql
mkdir /yum_local/php
mkdir /yum_local/apache
mkdir /yum_local/other
```

#### 修改yum配置

```
vim /etc/yum.conf

keepcache=1
# 保持yum安装的软件包

# sed -i "s#keepcache=0#keepcache=1#g" /etc/yum.conf
```

#### 安装createrepo

```
yum install createrepo -y
```

#### 生成rpm仓库索引

```
createrepo /yum_local/
```



### 安装及配置nginx

```
cat > /etc/yum.repos.d/nginx.repo << EOF
[nginx]
name=Nginx
baseurl=http://nginx.org/packages/centos/\$releasever/\$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
EOF
```

```
yum install nginx -y
```

```
cat > /etc/nginx/conf.d/yum_local.conf << EOF
server {
    listen 80;
    server_name 10.0.0.10;

    location / {
        root /yum_local;
        index index.html;
        autoindex on;
    } 
}
EOF
```

```
systemctl start nginx

systemctl enable nginx

systemctl status nginx
```



### 复制rpm软件包至本地yum仓库及更新

```
cp /var/cache/yum/x86_64/7/nginx/packages/* /yum_local/
```

```
createrepo --update /yum_local/
```



### 服务端下载nginx、php、mysql软件包

#### 下载nginx

```
cp /var/cache/yum/x86_64/7/nginx/packages/* /yum_local/
# 已安装nginx时用此方法

yum install --downloadonly --downloaddir=/yum_local/ nginx
# 未安装nginx时用此方法

# 两种方法的前提都是配置使用了nginx官方源之后进行操作
```

#### 下载php

```
# 配置清华remi源1

# yum localinstall https://mirrors.tuna.tsinghua.edu.cn/remi/enterprise/remi-release-7.rpm -y
```

```
# 配置清华remi源-2

cat > /etc/yum.repos.d/remi-safe.repo << EOF
# This repository is safe to use with RHEL/CentOS base repository
# it only provides additional packages for the PHP stack
# all dependencies are in base repository or in EPEL

[remi-safe]
name=Safe Remi's RPM repository for Enterprise Linux 7 - \$basearch
baseurl=https://mirrors.tuna.tsinghua.edu.cn/remi/enterprise/7/safe/\$basearch/
#baseurl=http://rpms.remirepo.net/enterprise/7/safe/\$basearch/
#mirrorlist=http://cdn.remirepo.net/enterprise/7/safe/mirror
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi
EOF

wget -O /etc/pki/rpm-gpg/RPM-GPG-KEY-remi https://rpms.remirepo.net/RPM-GPG-KEY-remi
```

```
yum install php73-php php73-php-cli php73-php-common php73-php-devel php73-php-embedded php73-php-json php73-runtime -y
```

```
yum install php73-php-gd php73-php-mbstring php73-php-pdo php73-php-xml -y
```

```
yum install php73-php-fpm php73-php-mysqlnd php73-php-opcache -y
```

```
yum install php73-php-pecl-memcached php73-php-pecl-redis5 php73-php-pecl-mongodb -y
```

```
# 需要安装的软件包有
php73-php
php73-php-cli
php73-php-common
php73-php-json
php73-runtime
php73-php-devel
php73-php-embedded
php73-php-gd
php73-php-mbstring
php73-php-pdo
php73-php-xml
php73-php-fpm
php73-php-mysqlnd
php73-php-opcache
php73-php-pecl-memcached
#php73-php-pecl-redis4
php73-php-pecl-redis5
php73-php-pecl-mongodb
#php71-php-mcrypt
#此包只有71版本
```

#### 下载mysql

```
# 使用清华源进行下载
# https://mirrors.tuna.tsinghua.edu.cn/mysql/
# https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/

cd /yum_local

wget https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/mysql-community-libs-5.7.26-1.el7.x86_64.rpm

wget https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/mysql-community-libs-compat-5.7.26-1.el7.x86_64.rpm

wget https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/mysql-community-common-5.7.26-1.el7.x86_64.rpm

wget https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/mysql-community-client-5.7.26-1.el7.x86_64.rpm

wget https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/mysql-community-server-5.7.26-1.el7.x86_64.rpm

# mysql服务需要下载4个包：
# mysql-client mysql-common mysql-libs mysql-server
# mysql-community-libs-compat是解决postfix的依赖（强制卸载mariadb后会丢失）
```

#### 复制软件包至本地yum源目录

```
cp -f /var/cache/yum/x86_64/7/base/packages/* /yum_local/
cp -f /var/cache/yum/x86_64/7/epel/packages/* /yum_local/
cp -f /var/cache/yum/x86_64/7/extras/packages/* /yum_local/
cp -f /var/cache/yum/x86_64/7/nginx/packages/* /yum_local/
cp -f /var/cache/yum/x86_64/7/remi-safe/packages/* /yum_local/
cp -f /var/cache/yum/x86_64/7/updates/packages/* /yum_local/
```

```
mv -f /yum_local/nginx*.rpm /yum_local/nginx/
mv -f /yum_local/php*.rpm /yum_local/php/
mv -f /yum_local/mysql*.rpm /yum_local/mysql/
mv -f /yum_local/*.rpm /yum_local/other/
```

```
createrepo --update /yum_local/
```



### 一键搭建脚本

#### onekey\_configure\_yum\_local\_lnmp.sh

```
#!/bin/bash

# make local yum repo dir
mkdir /yum_local -p
mkdir /yum_local/nginx
mkdir /yum_local/mysql
mkdir /yum_local/php
mkdir /yum_local/apache
mkdir /yum_local/other

# configure yum.conf, don't delete the packages after install software
sed -i 's#keepcache=0#keepcache=1#g' /etc/yum.conf

# add nginx official yum source
cat > /etc/yum.repos.d/nginx.repo << EOF
[nginx]
name=Nginx
baseurl=http://nginx.org/packages/centos/\$releasever/\$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
EOF


# add php tsinghua remi yum source

cat > /etc/yum.repos.d/remi-safe.repo << EOF
# This repository is safe to use with RHEL/CentOS base repository
# it only provides additional packages for the PHP stack
# all dependencies are in base repository or in EPEL

[remi-safe]
name=Safe Remi's RPM repository for Enterprise Linux 7 - \$basearch
baseurl=https://mirrors.tuna.tsinghua.edu.cn/remi/enterprise/7/safe/\$basearch/
#baseurl=http://rpms.remirepo.net/enterprise/7/safe/\$basearch/
#mirrorlist=http://cdn.remirepo.net/enterprise/7/safe/mirror
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi
EOF

wget -O /etc/pki/rpm-gpg/RPM-GPG-KEY-remi https://rpms.remirepo.net/RPM-GPG-KEY-remi


# create yum cache
yum clean all
yum makecache 

# install createrepo
yum install createrepo -y

# create local yum
createrepo /yum_local/

# install nginx
yum install nginx -y

# configure local yum nginx conf
cat > /etc/nginx/conf.d/yum_local.conf << EOF
server {
    listen 80;
    server_name 10.0.0.10;

    location / {
        root /yum_local;
        index index.html;
        autoindex on;
    } 
}
EOF

# start nginx service
systemctl start nginx
systemctl enable nginx


# copy software packages to local yum directory

# copy nginx
cp /var/cache/yum/x86_64/7/nginx/packages/* /yum_local/nginx/

createrepo --update /yum_local/


# install php
yum install php73-php php73-php-cli php73-php-common php73-php-devel php73-php-embedded php73-php-json php73-runtime -y
yum install php73-php php73-php-cli php73-php-common php73-php-devel php73-php-embedded php73-php-json php73-runtime -y

yum install php73-php-gd php73-php-mbstring php73-php-pdo php73-php-xml -y
yum install php73-php-gd php73-php-mbstring php73-php-pdo php73-php-xml -y

yum install php73-php-fpm php73-php-mysqlnd php73-php-opcache -y
yum install php73-php-fpm php73-php-mysqlnd php73-php-opcache -y

yum install php73-php-pecl-memcached php73-php-pecl-redis5 php73-php-pecl-mongodb -y
yum install php73-php-pecl-memcached php73-php-pecl-redis5 php73-php-pecl-mongodb -y


# 使用清华源进行下载
# https://mirrors.tuna.tsinghua.edu.cn/mysql/
# https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/

cd /yum_local

wget https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/mysql-community-libs-5.7.26-1.el7.x86_64.rpm

wget https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/mysql-community-libs-compat-5.7.26-1.el7.x86_64.rpm

wget https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/mysql-community-common-5.7.26-1.el7.x86_64.rpm

wget https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/mysql-community-client-5.7.26-1.el7.x86_64.rpm

wget https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql57-community-el7/mysql-community-server-5.7.26-1.el7.x86_64.rpm

# mysql服务需要下载4个包：
# mysql-client mysql-common mysql-libs mysql-server
# 卸载掉mariadb-lib后需要安装mysql-community-libs-compat，否则postfix无法启动

cp -f /var/cache/yum/x86_64/7/base/packages/* /yum_local/
cp -f /var/cache/yum/x86_64/7/epel/packages/* /yum_local/
cp -f /var/cache/yum/x86_64/7/extras/packages/* /yum_local/
cp -f /var/cache/yum/x86_64/7/nginx/packages/* /yum_local/
cp -f /var/cache/yum/x86_64/7/remi-safe/packages/* /yum_local/
cp -f /var/cache/yum/x86_64/7/updates/packages/* /yum_local/

mv -f /yum_local/nginx*.rpm /yum_local/nginx/
mv -f /yum_local/php*.rpm /yum_local/php/
mv -f /yum_local/mysql*.rpm /yum_local/mysql/
mv -f /yum_local/*.rpm /yum_local/other/

createrepo --update /yum_local/
```



### 客户端配置yum源

```
cat > /etc/yum.repos.d/yum_local.repo << EOF
[yum_local]
name=yum_local
baseurl=http://10.0.0.10
gpgcheck=0
enabled=1
EOF
```

```
yum clean all

yum makecache
```

```
yum list | grep nginx
```

