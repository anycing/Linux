# rewrite

> **rewrite主要功能是实现url地址重写，需要pcre软件的支持**
>
> **主要企业应用就是实现伪静态**



### rewrite功能在企业中的应用

> 1\. 可以调整用户浏览的URL，看起来更规范，合乎开发及产品人员的需求
>
> 2\. 为了让搜索引擎收录网站内容及用户体验更好，企业会将动态URL地址伪装成静态地址提供服务
>
> 3\. 网站换新域名后，让旧的域名访问跳转到新的域名上
>
> 根据特殊变量、目录、客户端的信息进行URL跳转



### 跳转实现

#### 实现将所有访问 will.com 的请求重定向到 www.will.com

```
server {
    listen       80;
    server_name  will.com;
    rewrite ^/(.*) http://www.will.com/$1 permanent;
}

server {
    listen       80;
    server_name  www.will.com;
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
```

#### 实现访问www.will.com/bbs/的请求跳转至bbs.will.com

```
server {
    listen       80;
    server_name  www.will.com;
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
    rewrite ^(.*)/bbs/ http://bbs.will.com break;
}
```
