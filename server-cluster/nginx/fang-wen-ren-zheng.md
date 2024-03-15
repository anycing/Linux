# 访问认证

### 安装加密工具

```
yum install httpd-tools -y

# 需要依赖httpd-tools进行加密
```

### 配置nginx

```
server {
    listen       80;
    server_name  www.will.com;
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
    auth_basic "hello will";
    auth_basic_user_file /etc/nginx/htpasswd;
}
```

### 添加访问的账号密码

```
htpasswd -cb /etc/nginx/htpasswd will 123456

# -c 创建密码文件
# -b 非交互模式


chmod 400 /etc/nginx/htpasswd
chown www /etc/nginx/htpasswd
```

### 重启并测试

```
systemctl restart nginx
```

