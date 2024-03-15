# Docker

### 安装docker

#### 换国内源

```bash
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

wget -O /etc/yum.repos.d/docker-ce.repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

yum makecache

# 使用清华源
# wget -O /etc/yum.repos.d/docker-ce.repo https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
# sed -i 's#download.docker.com#mirrors.tuna.tsinghua.edu.cn/docker-ce#g' /etc/yum.repos.d/docker-ce.repo
```

#### yum 安装

```
yum install docker-ce -y
```

#### 查看信息

```
docker version


# 启动服务端

systemctl start docker

systemctl enable docker


# 再次查看信息

docker version
```

#### 配置 docker 镜像加速

```
vim /etc/docker/daemon.json

{
    "registry-mirrors": ["https://2cky5149.mirror.aliyuncs.com"]
}
```

#### 重启并启动

```
systemctl restart docker

docker run -d -p 80:80 nginx

# run 创建并启动一个容器
# -d 放在后台
# -p 端口映射
# nginx 镜像的名称
```



### 镜像管理

#### 搜索镜像

```
docker search centos

# 搜索名称为 centos 的docker镜像
```

#### 拉取镜像

```
docker pull busybox

# 拉取名称为 busybox 的docker镜像
# 默认拉取最新版本
# 推送为 push


docker pull busybox:1.29

# 拉取指定版本的doker镜像
# 版本需要去官网查看，hub.docker.com
```

#### 查看镜像

```
docker image ls

# 查看本地有哪些镜像
```

#### 导出镜像

```
docker image save -o docker_busybox1.29.tar.gz busybox:1.29

# 导出版本号为1.29的busybox镜像到本地目录，导出名称为docker_busybox1.29.tar.gz
# -o 指定导出路径
```

#### 删除镜像

```
docker image rm busybox:1.29

# 删除指定版本的docker镜像
# 不接版本默认删除最新版
```

#### 导入镜像

```
docker image load -i docker_busybox1.29.tar.gz

# 导入当前目录下名称为docker_busybox1.29.tar.gz的镜像
# -i 指定镜像文件路径

# import 导入会丢掉镜像的标签，所以一般用 load 命令导入
```



### 容器常用命令

#### 创建并运行容器

```
docker run -it --name centos6 centos:6.9 /bin/bash

-it 分配交互式的终端 interactive tty
-d 在后台运行
-p 端口映射
-v 源地址（宿主机）：目标地址（容器）
--name 指定容器的名称
/bin/bash 覆盖容器的初始命令

# docker run = docker create + docker start
```

#### 查看容器

```
docker container ls

docker ps

#查看当前运行的容器


docker container ls -a -q

docker ps -a -q

# -a 查看所有状态容器（包括历史记录）
# -q 安静模式显示
# -l 最近一个容器
```

#### 启动停止容器

```
docker start centos6

# 可直接将 docker container ls -a 中已结束的容器启动
# centos6为上方命令中的容器名称


docker stop 0c810a1cbf32

# 数字为容器id
# 可在 docker container ls -a 中查看

# 名称和ID都可启动和停止容器


docker kill centos6

# 直接杀死容器进程
```

#### 删除容器

```
docker container rm centos6

# 删除名为 centos6 的容器


docker container rm `docker ps -a -q`

# 删除所有容器
# 可使用名称和ID删除
# -f 强制删除运行中的容器
```

#### 进入正在运行的容器

```
docker attach cf35142ac80a

# 使用同一个终端进入容器（两边终端会同步）
# Ctrl + p , Ctrl + q (可以偷偷离开)


docker container exec -it cf35142ac80a /bin/bash

# 重新分配一个终端进入指定容器
```

