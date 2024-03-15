# Basic Optimization

### 规范目录

```
mkdir -p /server/tools

mkdir -p /server/scripts
```



### 配置所有主机域名解析

```
cat >/etc/hosts<<EOF
127.0.0.1    localhost localhost.localdomain localhost4 localhost4.localdomain4
::1          localhost localhost.localdomain localhost6 localhost6.localdomain6
172.16.1.15  lb01
172.16.1.16  lb02
172.16.1.17  web01
172.16.1.18  web02
172.16.1.19  web03
172.16.1.31  nfs01
172.16.1.41  backup
172.16.1.51  db01 db01.etiantian.org
172.16.1.61  m01
EOF
```



### 修改主机名称

```
hostnamectl set-hostname <HOSTNAME>
```



### 更新yum源信息

```
# 换为阿里云的源

mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo



# 安装epel源

mv /etc/yum.repos.d/epel.repo /etc/yum.repos.d/epel.repo.backup

wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
```



### 优化安全设置

```
sed -i 's#SELINUX=.*#SELINUX=disabled#g' /etc/selinux/config
sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
grep SELINUX=disabled /etc/selinux/config
setenforce 0
getenforce

# 关闭SELinux
```

```
systemctl stop firewalld
systemctl disable firewalld
systemctl status firewalld

# 关闭firewalld防火墙
```



### 精简开机启动程序



### 设置普通用户提权操作（可选）

```
useradd will
echo 123456|passwd --stdin will
\cp /etc/sudoers /etc/sudoers.backup
echo "will  ALL=(ALL) NOPASSWD: ALL " >>/etc/sudoers
tail -1 /etc/sudoers
visudo -c
```



### 设置系统中文UTF8字符集（可选）

```
cp /etc/locale.conf /etc/locale.conf.backup
localectl set-locale LANG="zh_CN.UTF-8"
cat /etc/locale.conf
```



### 设置时间同步

```
cp /etc/chrony.conf{,backup}


cat >/etc/chrony.conf<<EOF
server ntp.aliyun.com iburst
stratumweight 0
driftfile /var/lib/chrony/drift
rtcsync
makestep 10 3
bindcmdaddress 127.0.0.1
bindcmdaddress ::1
keyfile /etc/chrony.keys
commandkey 1
generatecommandkey
logchange 0.5
logdir /var/log/chrony
EOF


firewall-cmd --add-service=ntp --permanent

firewall-cmd --reload


systemctl restart chronyd

systemctl enable chronyd



# 检查时区

timedatectl

timedatectl list-timezones

timedatectl set-timezone Asia/Shanghai
```



### 提升命令行操作安全性（可选）

```
echo 'export TMOUT=300' >>/etc/profile
echo 'export HISTSIZE=5' >>/etc/profile
echo 'export HISTFILESIZE=5'>>/etc/profile
tail -3 /etc/profile
. /etc/profile
```



### 加大文件描述符

```
echo '*              -      nofile          65535 ' >>/etc/security/limits.conf
tail -1 /etc/security/limits.conf
ulimit -SHn  65535
ulimit -n
```



### 优化系统内核

```
cat >>/etc/sysctl.conf<<EOF
net.ipv4.tcp_fin_timeout = 2
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_keepalive_time = 600
net.ipv4.ip_local_port_range = 4000    65000
net.ipv4.tcp_max_syn_backlog = 16384
net.ipv4.tcp_max_tw_buckets = 36000
net.ipv4.route.gc_timeout = 100
net.ipv4.tcp_syn_retries = 1
net.ipv4.tcp_synack_retries = 1
net.core.somaxconn = 16384
net.core.netdev_max_backlog = 16384
net.ipv4.tcp_max_orphans = 16384

# 以下参数是对iptables防火墙的优化，防火墙不开会提示，可以忽略不理。
net.nf_conntrack_max = 25000000
net.netfilter.nf_conntrack_max = 25000000
net.netfilter.nf_conntrack_tcp_timeout_established = 180
net.netfilter.nf_conntrack_tcp_timeout_time_wait = 120
net.netfilter.nf_conntrack_tcp_timeout_close_wait = 60
net.netfilter.nf_conntrack_tcp_timeout_fin_wait = 120
net.core.wmem_default = 8388608
net.core.rmem_default = 8388608net.core.wmem_max = 16777216
net.core.rmem_max = 16777216
EOF

sysctl -p
```



### 安装系统常用软件

```
yum install tree nmap dos2unix lrzsz nc lsof wget tcpdump htop iftop iotop sysstat nethogs -y

# CentOS6 和 CentOS7 都要安装的企业运维常用基础工具包
```

```
yum install psmisc net-tools bash-completion vim-enhanced -y

# CentOS7 要安装的企业运维常用基础工具包
```



### 优化 SSH 远程连接

```
# Port 52121                          # 监听端口（默认为22）
# PermitRootLogin no                  # 禁止 root 用户使用 ssh 登录

PermitEmptyPasswords no             # 禁止空密码登录
UseDNS no                           # 不使用 DNS 解析
GSSAPIAuthentication no             # 连接慢的解决配置

ListenAddress 192.168.1.17:52121    # 只允许内网ip从52121端口登录
# 上方的参数在配置完 VPN 后再加上，加上后可不需要修改原先默认端口和禁用root
# 此地址需要是本地的内网ip地址
```



### 保留 yum 安装包

```
vim /etc/yum.conf

# keepcache=0改为keepcache=1
# 保留yum安装包，方便日后内网一键部署
```



### 锁定关键系统文件

```
chattr +i /etc/passwd /etc/shadow /etc/group /etc/gshadow /etc/inittab

# 处理完后把chattr、lsattr改名并转移走，可提高安全性
```



### 去除系统及内核版本登录前的屏幕显示

```
>/etc/issue
>/etc/issue.net
```



### 清除多余的系统虚拟用户账号



### 为grub引导菜单加密码



### 禁止主机被ping



### 打补丁并升级有已知漏洞的软件

```
yum update
```



### 精简开机自启动服务

```
systemctl list-unit-files |grep enable|egrep -v "sshd.service|crond.service|chronyd.service|sysstat|rsyslog|^NetworkManager.service|irqbalance.service"|awk '{print "systemctl disable",$1}'|bash
```

```
systemctl list-unit-files | grep enabled
```

```
# 只保留以下服务：

# sshd
# crond
# chronyd
# sysstat
# rsyslog
# NetworkManager
# irqbalance

# 需要什么服务之后再添加
```



### 安装图形界面（根据需要）

```
yum groupinstall "Server with GUI"

# 安装图形桌面组件。


systemctl set-default graphical.target

# 设置默认启动级别为graphical.target


systemctl start graphical.target

# 启动graphical.target
```



### 企业生产最小化原则：

1. 安装软件包最小化
2. 用户权限最小化
3. 目录文件权限最小化
4. 自启动服务最小化
5. 服务运行用户权限最小化

