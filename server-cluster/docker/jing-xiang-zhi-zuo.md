# 镜像制作一

> **以基于 CentOS6.9 制作 nginx 镜像为例**

####

### 启动 CentOS6.9 容器

```
docker run -it -p 81:80 centos:6.9 /bin/bash
```

###

### 进入容器操作

#### 换源

```
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo

curl -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-6.repo

# 由于镜像是 CentOS6.9 此处更好为阿里云 CentOS6 的源

yum clean all

yum makecache
```

#### yum安装nginx

```
yum install nginx -y
```

#### 测试nginx

```
nginx -g 'daemon off;'

curl 192.168.1.61:81
```

#### 退出容器

```
Ctrl + c

exit
```



### 提交镜像

```
docker container ls -a


docker container commit 16d1fba8bb13 centos6.9_nginx:v1

# 把刚才的容器文件（此处用的是ID）提交成名字为 centos6.9_nginx:v1 的镜像
```

#### 查看镜像

```
docker images
```

#### 测试运行情况

```
docker run -d -p 82:80 centos6.9_nginx:v1 nginx -g 'daemon off;'
```
