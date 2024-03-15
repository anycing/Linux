# Configure Optimization

## 使用单个独立配置文件（非主配置文件）

### 原配置文件如下

```
cat /application/nginx/conf/nginx.conf
```

```
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
        server_name  www.imaginist.xyz;
        location / {
            root   html/www;
            index  index.html index.htm;
        }
    }
    server {
        listen       80;
        server_name  blog.imaginist.xyz;
        location / {
            root   html/blog;
            index  index.html index.htm;
        }
    }
    server {
        listen       80;
        server_name  bbs.imaginist.xyz;
        location / {
            root   html/bbs;
            index  index.html index.htm;
        }
    }
}
```

### 建立配置目录并写入配置

```
cd /application/nginx/conf/

mkdir extra


sed -n "10,17p" nginx.conf > extra/01_www.conf

sed -n "28,25p" nginx.conf > extra/02_blog.conf

sed -n "26,33p" nginx.conf > extra/03_bbs.conf
```

```
sed -n "10,33p" nginx.conf

sed -i "10,33d" nginx.conf

sed -i "10i include extra/01_www.conf;\ninclude extra/02_blog.conf;\ninclude extra/03_bbs.conf;" nginx.conf
```



### 检查并启动

```
nginx -t

nginx -s reload
```

#### nginx 停止和启动命令

```
nginx -s stop

nginx
```



### 终极方法

```
include extra/*.conf;
```
