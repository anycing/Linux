# Software Installation

### 软件包格式及管理器

| System                 | Format | Package Manager |
| ---------------------- | ------ | --------------- |
| CentOS  RedHat  Fedora | rpm    | yum             |
| Debian  Ubuntu         | deb    | apt             |

###

### rpm & yum 安装命令

#### rpm

```bash
rpm

# -q 查询软件包
# -qa 查询所有安装的软件包
# -i 安装软件包
# -e 卸载软件包

#    e.g.
#        rpm -q vim-common
#        rpm -qa | grep vim
#        rpm -i vim-common-7.4.160-5.el7.x86_64.rpm
#        rpm -e vim-common

# -q -e 只需软件名称（不带版本等信息，但也不能简写或缩小）
# -i 则需要软件完整名称（带版本、平台等信息）
# -i -e 后可接多个软件名称，以空格分隔


# NAME: RPM Package Manager

# rpm -ql <software_name>
# 查询软件包包含的文件

# rpm -qf <file_name>
# 查询文件属于哪个软件包
```

#### yum

```bash
yum 

# install 安装软件包
# remove 卸载，完全移除软件包，包括所有依赖项（禁止使用）
# list 查看软件包，可以单独查看所有已安装，可升级，可用的软件包
# grouplist 查看软件组包
# groupinstall 安装软件组包
# update 升级软件

#    e.g.
#        yum install PACKAGE1 PACKAGE2
#        yum remove PACKAGE
#        yum list
#            yum list installed
#            yum list updates
#            yum list available
#        yum grouplist
#        yum groupinstall GROUPPACKAGE1 GROUPPACKAGE2
#        yum update
#        yum update PACKAGE

# -q 安静模式，不显示安装过程
# -y 当安装过程提示选择全部为"yes"
```



### dpkg & apt

#### dpkg

```bash
# 安装
dpkg -i package-name.deb

# 移除
dpkg -r package-name

# 查询
dpkg -l package-name

```

#### apt-get

```bash
# 安装软件包
apt-get install <package_name>
# 安装指定版本的软件包
sudo apt-get install kubeadm=1.21.1-00 -y

# 重新安装软件包
apt-get install --reindtall <package_name>

# 卸载软件（不会删除依赖软件包，且保留配置文件）
apt-get remove <package_name>
# 卸载软件，不保留配置文件，删除软件安装包，同时删除相应依赖软件包。
apt-get remove --purge <package_name>
apt-get purge <package_name>

# 更新系统中所有的包到最新版
apt-get upgrade
```

#### apt-cache

```bash
# 搜索软件包
apt-cache search <package_name>

# 显示指定软件包的详细信息
apt-cache show <package_name>

# 查找软件包的依赖关系
apt-cache depends <package_name>
```

```
apt-cache madison kubeadm
```

#### apt-mark

```bash
# hold 用于将一个包标记为被阻止，这将阻止该包被自动安装、升级或删除。
apt-mark hold kubeadm

# unhold 用于取消先前对包设置的保留，以再次允许所有操作。
apt-mark unhold kubeadm
```

#### 软件包缓存所在目录

```bash
# Cache directory
/var/cache/apt/archives/
```



### 国内软件源镜像站点

* [https://opsx.alibaba.com/mirror](https://opsx.alibaba.com/mirror)
* [http://mirrors.163.com](http://mirrors.163.com/)
* [https://mirrors.cloud.tencent.com](https://mirrors.cloud.tencent.com/)



### 配置yum源

```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

# Backup repo files
```

#### 添加阿里云软件源

#### Base

```
# CentOS 5
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo


# CentOS 6
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo


# CentOS 7
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

# Download and save repo file
```

#### epel

```
# CentOS 5
wget -O /etc/yum.repos.d/epel-5.repo http://mirrors.aliyun.com/repo/epel-5.repo
curl -o /etc/yum.repos.d/epel-5.repo http://mirrors.aliyun.com/repo/epel-5.repo


# CentOS 6
wget -O /etc/yum.repos.d/epel-6.repo http://mirrors.aliyun.com/repo/epel-6.repo
curl -o /etc/yum.repos.d/epel-6.repo http://mirrors.aliyun.com/repo/epel-6.repo


# CentOS 7
wget -O /etc/yum.repos.d/epel-7.repo http://mirrors.aliyun.com/repo/epel-7.repo
curl -o /etc/yum.repos.d/epel-7.repo http://mirrors.aliyun.com/repo/epel-7.repo
```

```
yum clean all
yum makecache

# Rebuild yum cache
```

```
yum repolist

# 列出启用的yum源

yum repolist all

# 列出所有的yum源，包含禁用的yum源
```

#### 安装epel源扩展软件仓库

```
yum install epel-release -y
```

#### 添加nginx官方源

```
cat > /etc/yum.repos.d/nginx.repo << EOF
[nginx]
name=Nginx
baseurl=http://nginx.org/packages/centos/\$releasever/\$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
EOF
```

#### 添加Mariadb官方源

```
vim /etc/yum.repos.d/MariaDB.repo

# 配置信息如下：

[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.4/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

```
yum install MariaDB-server MariaDB-client -y
```



### 配置apt源

#### debian 7.x (wheezy)

```
vim /etc/apt/sources.list

deb http://mirrors.aliyun.com/debian/ wheezy main non-free contrib
deb http://mirrors.aliyun.com/debian/ wheezy-proposed-updates main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ wheezy main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ wheezy-proposed-updates main non-free contrib
```

#### debian 8.x (jessie)

```
vim /etc/apt/sources.list

deb http://mirrors.aliyun.com/debian/ jessie main non-free contrib
deb http://mirrors.aliyun.com/debian/ jessie-proposed-updates main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ jessie main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ jessie-proposed-updates main non-free contrib

```

#### debian 9.x (stretch)

```
vim /etc/apt/sources.list

deb http://mirrors.aliyun.com/debian/ stretch main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ stretch main non-free contrib
deb http://mirrors.aliyun.com/debian-security stretch/updates main
deb-src http://mirrors.aliyun.com/debian-security stretch/updates main
deb http://mirrors.aliyun.com/debian/ stretch-updates main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ stretch-updates main non-free contrib
deb http://mirrors.aliyun.com/debian/ stretch-backports main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ stretch-backports main non-free contrib
```
