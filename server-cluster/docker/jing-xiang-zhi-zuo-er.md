# 镜像制作二

> **以基于 CentOS6.9 制作 nginx + php 环境 镜像为例（基础镜像制作一）**

### 启动容器

```
docker run -it -p 82:80 centos6.9_nginx:v1 /bin/bash
```

####

### 进入容器操作

#### yum安装php

```
yum install php-fpm -y
```

#### 配置php

```
vi /etc/php-fpm.d/www.conf 


user = nginx

group = nginx
```

#### &#x20; 启动php服务

```
service php-fpm start
```

#### 配置nginx支持php

```
grep -Ev "^$|#" /etc/nginx/nginx.conf.default > /etc/nginx/nginx.conf

worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       80;
        server_name  localhost;
        location / {
            root   /code;
            index  index.php index.html index.htm;
        }
        location ~ \.php$ {
            root           /code;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
    }
}
```

```
# nginx支持php参数

location ~ \.php$ {
        root           html;            #此为网站主目录，需修改
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
```

#### 测试nginx配置

```
nginx -t
```

#### 下载项目所需代码

```
mkdir /code

# 配置文件中设置的此目录为网站主目录


cd /code

curl -o kodexplorer4.40.zip http://static.kodcloud.com/update/download/kodexplorer4.40.zip


yum install unzip -y

unzip kodexplorer4.40.zip


chown -R nginx:nginx ./
# 修改当前目录及文件的权限属主和属组为nginx
```

#### 启动nginx

```
service nginx start
```



#### 访问测试

```
# 浏览器访问 192.168.1.61:82

# 提示 error: 
# Php library missing mb_string
# php GD is not enabled

yum install php-mbstring php-gd -y
```

#### 重启php

```
service php-fpm restart

# 继续访问测试，没问题则 exit 退出
```



### 制作镜像

```
docker container commit 63f37568f239 kod:v1

docker images
```

{% hint style="danger" %}
此时的镜像无法hang住，需进一步制作&#x20;
{% endhint %}

#### 启动刚才的容器并进入

```
docker start 63f37568f239

docker attach 63f37568f239
```

#### 写入脚本

```
vi /init.sh


#!/bin/bash

service php-fpm start

nginx -g 'daemon off;'
```

#### 执行脚本测试

```
sh /init.sh

# 然后访问测试，测试没有问题退出
```

#### 再次提交镜像v2版本

```
docker container commit 63f37568f239 kod:v2
```

#### 启动最新的镜像容器测试

```
docker run -d -p 84:80 kod:v2 /bin/bash /init.sh
```
