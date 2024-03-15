# Nginx

## 安装Nginx

### yum 安装

#### 添加nginx官方源

```
vim /etc/yum.repos.d/Nginx.repo

# 配置信息如下：

[nginx]
name=Nginx
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
```

#### 安装并启动服务

```
yum install nginx -y


systemctl start nginx

systemctl enable nginx

systemctl status nginx


curl 192.168.1.200
```



### 编译安装

#### 下载源码

```
mkdir -p /server/tools

cd /server/tools

wget http://nginx.org/download/nginx-1.16.0.tar.gz
```

#### 安装编译器和依赖

```
yum install gcc make pcre-devel openssl-devel -y
```

#### 解压并创建所需用户

```
tar xf nginx-1.16.0.tar.gz

cd nginx-1.16.0/

useradd -s /sbin/nologin www -M 

id www

# 此用户用于编译安装nginx时使用
```

#### 配置源码并编译安装

```
./configure  --user=www --group=www --prefix=/application/nginx-1.16.0/ --with-http_stub_status_module  --with-http_ssl_module --with-pcre --with-stream

make && make install


# configure 参数的作用
# --prefix=PATH  路径
# --user=USER    用户
# --group=GROUP  组
# --with-pcre    伪静态
# --with-http_stub_status_module  状态
# --with-http_ssl_module  ssl加密
# --with-stream 实现四层协议的转发、代理或者负载均衡等
```

#### 启动服务

```
ln -s /application/nginx-1.16.0/ /application/nginx

/application/nginx/sbin/nginx
```



### 防火墙放行

```
firewall-cmd --permanent --add-service=http

firewall-cmd --permanent --add-service=https

firewall-cmd --reload
```

