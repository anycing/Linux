# 一键yum安装

{% hint style="success" %}
**此方法为局域网本地搭建yum源进行安装**
{% endhint %}



### 客户端配置yum源

```
cat > /etc/yum.repos.d/yum_local.repo << EOF
[yum_local]
name=yum_local
baseurl=http://192.168.1.61
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

### 添加用户

```
useradd -u 1111 -s /sbin/nologin -M www
```

### 安装nginx

```
yum install nginx -y

systemctl start nginx

systemctl enable nginx
```

#### 修改nginx默认用户

```
sed -i "s#user  nginx;#user  www;#g" /etc/nginx/nginx.conf
```



### 安装php

```
yum install php73-php php73-php-cli php73-php-common php73-php-devel php73-php-embedded php73-php-json php73-runtime -y

yum install php73-php-gd php73-php-mbstring php73-php-pdo php73-php-xml -y

yum install php73-php-fpm php73-php-mysqlnd php73-php-opcache -y

yum install php73-php-pecl-memcached php73-php-pecl-redis5 php73-php-pecl-mongodb -y
```

#### 备份配置php.ini

```
cp /etc/opt/remi/php73/php.ini{,.backup}
```

#### 备份及配置php-fpm

```
cp /etc/opt/remi/php73/php-fpm.conf{,.backup}

cp /etc/opt/remi/php73/php-fpm.d/www.conf{,.backup}


# 修改php-fpm用户为www
sed -i "s#user = apache#user = www#g" /etc/opt/remi/php73/php-fpm.d/www.conf
sed -i "s#group = apache#group = www#g" /etc/opt/remi/php73/php-fpm.d/www.conf
```

#### 配置nginx支持php

```
mv /etc/nginx/conf.d/default.conf{,.backup}

cat > /etc/nginx/conf.d/www.conf << EOF
server {
    listen       80;
    server_name  www.will.com;

    root   /usr/share/nginx/html/www;

    location / {
        index  index.php index.html index.htm;
    }
    
    error_page  404              /404.html;

    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME \$document_root\$fastcgi_script_name;
        include        fastcgi_params;
    }
}
EOF
```

#### 添加网站测试代码

```
# create website test code
mkdir -p /usr/share/nginx/html/www
chown -R www.www /usr/share/nginx/html/www

cd /usr/share/nginx/html/www

echo "hello php index" > index.php

echo "<?php phpinfo(); ?>" > info.php
```

#### 启动php-fpm

```
systemctl start php73-php-fpm

systemctl enable php73-php-fpm
```

#### 重启nginx

```
systemctl restart nginx
```



### 安装MySQL

```
# 卸载mariadb，避免起冲突
rpm -e --nodeps mariadb-libs

# 卸载后会导致postfix无法启动，提示缺少libmysqlclient.so.18(libmysqlclient_18)(64bit)
# 可安装mysql-community-libs-compat包解决


yum install mysql-community-server -y

# 安装后临时默认密码查看方式：
# grep "temporary password" /var/log/mysqld.log

# 可使用 mysql_secure_installation 进行安全初始化
```

```
systemctl start mysqld

systemctl enable mysqld
```



### 一键安装脚本

#### onekey\_install\_nginx\_php\_by\_yum.sh

```
#!/bin/bash

# configure local yum repo
cat > /etc/yum.repos.d/yum_local.repo << EOF
[yum_local]
name=yum_local
baseurl=http://192.168.1.61
gpgcheck=0
enabled=1
EOF

yum clean all
yum makecache


# add user www for web service
useradd -u 1111 -s /sbin/nologin -M www


# install nginx and configure
yum install nginx -y
systemctl start nginx
systemctl enable nginx

sed -i "s#user  nginx;#user  www;#g" /etc/nginx/nginx.conf


# install php
yum install php73-php php73-php-cli php73-php-common php73-php-devel php73-php-embedded php73-php-json php73-runtime -y

yum install php73-php-gd php73-php-mbstring php73-php-pdo php73-php-xml -y

yum install php73-php-fpm php73-php-mysqlnd php73-php-opcache -y

yum install php73-php-pecl-memcached php73-php-pecl-redis5 php73-php-pecl-mongodb -y


# backup php configuration
cp /etc/opt/remi/php73/php.ini{,.backup}
cp /etc/opt/remi/php73/php-fpm.conf{,.backup}
cp /etc/opt/remi/php73/php-fpm.d/www.conf{,.backup}

# modify php-fpm user www
sed -i "s#user = apache#user = www#g" /etc/opt/remi/php73/php-fpm.d/www.conf
sed -i "s#group = apache#group = www#g" /etc/opt/remi/php73/php-fpm.d/www.conf


# configure nginx support php
mv /etc/nginx/conf.d/default.conf{,.backup}

cat > /etc/nginx/conf.d/www.conf << EOF
server {
    listen       80;
    server_name  www.will.com;

    root   /usr/share/nginx/html/www;
    
    error_page  404              /404.html;

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


# create website test code
mkdir -p /usr/share/nginx/html/www
chown -R www.www /usr/share/nginx/html/www

cd /usr/share/nginx/html/www
echo $(hostname -I) > index.php
echo "<br><br>" >> index.php
echo "<?php phpinfo(); ?>" >> index.php

echo "status: 404" > 404.html
echo "<br><br>" >> 404.html
echo $(hostname -I) >> 404.html


# start php-fpm
systemctl start php73-php-fpm
systemctl enable php73-php-fpm


# restart nginx
systemctl restart nginx
```

