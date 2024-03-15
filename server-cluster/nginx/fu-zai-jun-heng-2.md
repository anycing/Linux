# 负载均衡-2

## 根据URL中的目录地址实现代理转发

### 需求说明

> 当用户请求 www.will.com/upload/x 地址时，实现由上传服务器池（upload\_pools）处理请求；
>
> 当用户请求 www.will.com/static/x 地址时，实现由静态服务器池（static\_pools）处理请求；
>
> 除此以外，对于其它访问请求全部由默认动态服务器池（default\_pools）处理请求。

### 需求图示

![动静分离网站集群架构](../../.gitbook/assets/动静分离网站集群架构.jpeg)

### 所需服务器列表

| 服务器ip        | 主机名   | 用途                                   |
| ------------ | ----- | ------------------------------------ |
| 192.168.1.15 | lb01  | nginx 负载均衡 （此实验中需要进行配置）              |
| 192.168.1.17 | web01 | nginx web static （假设已提前配置好）          |
| 192.168.1.18 | web02 | nginx web upload （假设已提前配置好）          |
| 192.168.1.19 | web03 | nginx web default dynamic （假设已提前配置好） |
| 192.168.1.61 | m01   | 管理机，局域网本地yum源（假设已提前配置好）              |

{% hint style="success" %}
以下操作均在 **lb01** 上完成
{% endhint %}

### nginx.conf 配置文件内容 （cat /etc/nginx/nginx.conf）

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
    
    upstream static_pools {
        server 192.168.1.17:80 weight=1;
    }
    
    upstream upload_pools {
        server 192.168.1.18:80 weight=1;
    }
    
    upstream default_pools {
        server 192.168.1.19:80 weight=1;
    }
    
    server {
        listen       80;
        server_name  www.will.com;
        
        location / {
            proxy_pass http://default_pools;
            include proxy.conf;
        }
        
        location /static/ {
            proxy_pass http://static_pools;
            include proxy.conf;
        }
        
        location /upload/ {
            proxy_pass http://upload_pools;
            include proxy.conf;
        }
    }
}
```

### proxy.conf 配置文件内容 （cat /etc/nginx/proxy.conf）

```
proxy_set_header Host  $host;
proxy_set_header X-Forwarded-For $remote_addr;
proxy_connect_timeout 60;
proxy_send_timeout 60;
proxy_read_timeout 60;
proxy_buffer_size 4k;
proxy_buffers 4 32k;
proxy_busy_buffers_size 64k;
proxy_temp_file_write_size 64k;
```

### onekey\_install\_nginx\_proxy.sh

> 此脚本执行于 **lb01** 上

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

# install nginx
yum install nginx -y


# configure nginx
mv /etc/nginx/nginx.conf{,.backup}


cat > /etc/nginx/nginx.conf << EOF
worker_processes  1;
events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    
    upstream static_pools {
        server 192.168.1.17:80 weight=1;
    }
    
    upstream upload_pools {
        server 192.168.1.18:80 weight=1;
    }
    
    upstream default_pools {
        server 192.168.1.19:80 weight=1;
    }
    
    server {
        listen       80;
        server_name  www.will.com;
        
        location / {
            proxy_pass http://default_pools;
            include proxy.conf;
        }
        
        location /static/ {
            proxy_pass http://static_pools;
            include proxy.conf;
        }
        
        location /upload/ {
            proxy_pass http://upload_pools;
            include proxy.conf;
        }
    }
}
EOF

cat > /etc/nginx/proxy.conf << EOF
proxy_set_header Host  \$host;
proxy_set_header X-Forwarded-For \$remote_addr;
proxy_connect_timeout 60;
proxy_send_timeout 60;
proxy_read_timeout 60;
proxy_buffer_size 4k;
proxy_buffers 4 32k;
proxy_busy_buffers_size 64k;
proxy_temp_file_write_size 64k;
EOF


# start nginx
systemctl start nginx
systemctl enable nginx
```
