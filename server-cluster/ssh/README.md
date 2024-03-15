# SSH

{% hint style="success" %}
**以下操作在服务端完成**
{% endhint %}

### 安装软件

```
yum install openssh openssl -y


systemctl start sshd

systemctl enable sshd
```

### 配置文件说明

```
# sshd是服务端配置文件

cat /etc/ssh/sshd_config


# ssh是客户端配置文件

cat /etc/ssh/ssh_config
```

#### sshd 服务配置优化

```
# sshd 服务配置优化
cat /etc/ssh/sshd_config

Port 52121                          # 监听端口（默认为22）
PermitRootLogin no                  # 禁止 root 用户使用 ssh 登录
PermitEmptyPasswords no             # 禁止空密码登录
UseDNS no                           # 不使用 DNS 解析
GSSAPIAuthentication no             # 连接慢的解决配置

# 下方的参数在配置完 VPN 后再加上，加上后可不需要修改原先默认端口和禁用root
ListenAddress 192.168.1.61:52121    # 内网登录跳板机
# 此地址需要是本地的内网ip地址
```





{% hint style="success" %}
**以下操作在客户端完成**
{% endhint %}

### 客户端工具

```
yum install openssh-clients -y


rpm -ql openssh-clients

# 查看 openssh-clients 软件包里的文件

/usr/bin/scp            # 远程拷贝文件
/usr/bin/sftp            # ftp 服务，加密传输文件
/usr/bin/ssh            # 远程连接
/usr/bin/ssh-copy-id    # 拷贝秘钥中的公钥文件
```

```
# ssh 命令示例

ssh -p 22 root@192.168.1.17
```

```
# scp 命令示例

# 推
scp -P 22 -rp /data root@192.168.1.17:/tmp
# 拉
scp -P 22 -rp root@192.168.1.17:/tmp/data /tmp

# -P port
# -p 保留时间和权限属性
# -r 递归复制整个目录
# -l 限速，单位Kbit/s
```

```
# sftp 命令示例

sftp -P 22 root@192.168.1.17

> put /etc/hosts /tmp
# 上传本地的 /etc/hosts 文件至远端的 /tmp 目录

> get /etc/hosts /tmp
# 下载远端的 /etc/hosts 文件至本地 /tmp 目录
```

{% hint style="info" %}
与服务器传输数据可用工具：

scp / rz sz ( lrzsz ) / sftp
{% endhint %}



### SSH 连不上的排错方法

```
# 物理链路
ping ip

# 服务器端口与服务是否开启
telnet ip port
firewall

# 检查本地 ssh 连接的相关配置
```



### SSH 认证方式

**① 密码认证**

#### 本地公钥保存位置（密码验证接受来自服务端的公钥）

```
cat ~/.ssh/known_hosts

# 家目录下的.ssh目录下，此文件为ssh建立连接后服务端发给客户端的（理解为：锁）


ssh-keygen -R 172.168.1.17

# 客户端清除指定 IP 旧的公钥信息
```



#### ② 秘钥认证

#### 生成秘钥对

```
ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_cluster -q

# 此命令可不交互生成秘钥对
# -t 指定秘钥类型
# -b 指定长度
# -N 指定秘钥的密码，此示例为空
# -f 指定生成秘钥的位置和文件名
# -q 不输出信息
```

#### 分发公钥

```
ssh-copy-id -i ~/.ssh/id_cluster.pub root@172.16.1.17

# 也可手动追加公钥信息至远端 ~/.ssh/authorized_keys 文件中
# 手动添加后需要修改文件权限为600，所在文件夹权限为700


# 可在远端查看是否发送成功
# cat ~/.ssh/authorized_keys
```

#### 客户端远程连接

```
ssh -i ~/.ssh/id_rsa 172.16.1.17

# -i 指定私钥
```
