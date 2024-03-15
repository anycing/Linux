# dockerfile2

### create dockerfile

```
mkdir /server/docker/nginx_ssh -p

cd /server/docker/nginx_ssh
```

```
vim dockerfile

# dockerfile 内容如下

FROM centos:6.9
RUN curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
RUN curl -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-6.repo
RUN yum makecache
RUN yum install nginx -y
RUN yum install openssh-server -y
EXPOSE 80 22
ENV SSH_PASS=123456
ADD init.sh /
ENTRYPOINT ["/bin/bash","/init.sh"]
```

{% hint style="danger" %}
**ADD 与 COPY 的文件需放在当前目录下，否则会提示找不到文件**
{% endhint %}

```
vim init.sh

#!/bin/bash

if [ ! -z $1 ]; then
    SSH_PASS=$1
fi
echo "$SSH_PASS" | passwd --stdin root
service sshd start
nginx -g 'daemon off;'
```



### build docker image

```
docker image build -t centos6.9_nginx_ssh:v3 /server/dockerfile/nginx_ssh/
```



### run container & test

```
docker run -d -P centos6.9_nginx_ssh:v3 centos69
```

```
docker container ls -l
```

```
curl 192.168.1.61:Port1
```

```
ssh root@192.168.1.61 -p Port2

> centos69
```
