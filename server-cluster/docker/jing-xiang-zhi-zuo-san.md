# 镜像制作三

> **以基于 CentOS6.9 制作 nginx + php + mysql 环境镜像为例（基础镜像制作二）**

### 启动容器

```
docker run -it -p 85:80 kod:v2 /bin/bash
```

###

### 进入容器操作

#### 启动nginx和php服务

```
service nginx start

service php-fpm start
```

#### 下载MySQL

```
yum install mysql-server -y
```

#### 启动MySQL

```
service mysqld start


# 检查服务启动情况
netstat -ntlp
```

#### 安装php-mysqli模块

```
yum install php-mysqli -y
```

#### 下载网站源码

```
cd /code

rm -rf *


curl -o phpwind.zip xxxxx

unzip phpwind.zip
```

#### 授权代码目录

```
chown -R nginx:nginx ./
```

#### 创建MySQL数据库

```
mysql

> create database phpwind;
```

```
mysqladminn -uroot password "123456"
```

#### 重启php服务

```
service php-fpm restart
```

#### 安装网站代码

```
# 浏览器访问 http://192.168.1.61:85/
```

#### 修改初始脚本

```
vi /init.sh


#!/bin/bash

service mysqld start
service php-fpm start
nginx -g 'daemon off;'
```

```
exit

# 退出容器
```



### 制作镜像

```
docker container commit b528573f1ada phpwind:v1
```

#### 启动测试

```
docker run -d -p 86:80 phpwind:v1 /bin/bash /init.sh

docker container ls
```

#### 访问测试

```
# 浏览器访问 192.168.1.61:86
```
