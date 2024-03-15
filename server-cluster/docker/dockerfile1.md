# dockerfile1

### 创建dockerfile文件

```
mkdir -p /server/dockerfile/nginx

cd /server/dockerfile/nginx

vim dockerfile
```

#### dockerfile文件内容如下

```
FROM centos:6.9
RUN curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
RUN curl -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-6.repo
RUN yum clean all
RUN yum makecache
RUN yum install nginx -y
CMD ["nginx","-g","daemon off;"]
```



### 自动生成镜像

```
docker image build -t centos6.9_nginx:v2 /server/dockerfile/nginx/

# -t 指定tag
# 最后的目录为dockerfile所在的目录
```



### 启动测试

```
docker run -d -p 80:80 centos6.9_nginx:v2
```

```
# 浏览器访问 192.168.1.61
```
