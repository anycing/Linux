# Virtual Host

### 基于域名的虚拟主机

#### 准备站点目录

```
mkdir -p /application/nginx/html/www

mkdir -p /application/nginx/html/blog

mkdir -p /application/nginx/html/bbs
```

#### 编辑配置文件

```
egrep -v "^$|#" nginx.conf.default > nginx.conf


vim /application/nginx/conf/nginx.conf

# 最终配置如下

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

# 修改配置如下：
# server_name  www.imaginist.xyz;
# root   html/www;
```

#### 修改默认主页文件

```
echo "hello, imaginist.xyz" > /application/nginx/html/www/index.html

echo "hello, blog.imaginist.xyz" > /application/nginx/html/blog/index.html

echo "hello, bbs.imaginist.xyz" > /application/nginx/html/bbs/index.html
```

#### 添加 hosts 解析

```
echo "192.168.1.200 www.imaginist.xyz blog.imaginist.xyz bbs.imaginist.xyz" >> /etc/hosts
```

#### 添加 nginx 目录至环境变量

```
echo 'PATH="/application/nginx/sbin:$PATH"' >> /etc/profile
```

#### 检查并重启

```
nginx -t

nginx -s reload
```

#### 访问测试

```
curl www.imaginist.xyz

curl blog.imaginist.xyz

curl bbs.imaginist.xyz
```

#### 使用别名

```
    server {
        listen       80;
        server_name  www.imaginist.xyz www1.imaginist.xyz;
        location / {
            root   html/www;
            index  index.html index.htm;
        }
    }
    
# server_name 里配置多个域名指向同一台主机
# 常用于负载均衡的集群的监控，这里使用www1别名以区别
```



### 基于端口的虚拟主机

```
# 仅需修改 listen 80为81
    server {
        listen       81;
        server_name  blog.imaginist.xyz;
        location / {
            root   html/blog;
            index  index.html index.htm;
        }
    }
```

#### 检查并重启

```
nginx -t

nginx -s reload
```

#### 访问测试

```
curl blog.imaginist.xyz:81
```

