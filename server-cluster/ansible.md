# Ansible

### 安装软件

```
yum install ansible -y

yum install libselinux-python -y

# ansible 需要 epel 源
# yum install epel-release -y

# 客户端需安装 libselinux-python
```



### 常用参数说明

| 参数            | 说明                               |
| ------------- | -------------------------------- |
| -m            | 模块名                              |
| -a            | 模块的参数                            |
| -f            | 并发数                              |
| -i            | 指定主机列表文件（默认为 /etc/ansible/hosts） |
| --private-key | 指定验证的私钥文件                        |

#### 命令使用示例

```
ansible web_server --private-key ~/.ssh/id_cluster -m command -a "free -m"

# web_server 为主机列表配置文件里的模块名称
```



### 主机列表配置

```
cp /etc/ansible/hosts{,.backup.ori}

# 备份原始配置文件
```

```
cat > /etc/ansible/hosts << EOF
[web_server]
172.16.1.17
172.16.1.18

[nfs_server]
172.16.1.31

[backup_server]
172.16.1.41
EOF
```

#### 带密码验证信息的写法

```
[cluster]
172.16.1.31 ansible_ssh_user=root ansible_ssh_pass=centos7 ansible_ssh_port=52121
172.16.1.41 ansible_ssh_user=root ansible_ssh_pass=centos7 ansible_ssh_port=52121

# 带用户名、密码、端口的配置写法
```

###

### 主配置文件

```
cp /etc/ansible/ansible.cfg{,.backup.ori}

# 备份原始配置文件
```

```
cat /etc/ansible/ansible.cfg


host_key_checking = False
# 取消新主机key文件检查（首次连接主机不用输yes）
```

#### 使用 sed 命令一键完成

```
sed -i 's/#host_key_checking = False/host_key_checking = False/g' /etc/ansible/ansible.cfg
```

###

### 执行命令

#### 使用密码

```
# 先将 ssh 连接的用户名和密码写入主机配置文件

cat /etc/ansible/hosts

[cluster_pass]
172.16.1.31 ansible_ssh_user=root ansible_ssh_pass=centos7
172.16.1.41 ansible_ssh_user=root ansible_ssh_pass=centos7

#所有用户的账号密码
[all:vars]
ansible_user=root
ansible_password=123456


ansible cluster_pass -m command -a "free -m"
```

#### 使用私钥

```
ansible backup_server --private-key ~/.ssh/id_cluster -m command -a "free -m"

# 使用私钥免密认证需加 --private-key 参数
# --private-key ~/.ssh/id_cluster
```

#### -m command 模块说明

```
# 不支持 > < | & 等 符号
# 如需要使用则可以自写Shell替代
```



### 模块使用

#### 查看模块

```
ansible-doc -l
```

#### 查看指定模块的帮助信息

```
ansible-doc -s MODULE

# 查看 MODULE 模块的帮助信息
```

#### command 模块

```
ansible web_server -m command -a "hostname -I"
```

#### shell 模块

```
ansible web_server -m shell -a "/server/scripts/time.sh"

# Shell脚本需要存在于远程主机上
```

#### copy 模块

```
ansible web_server -m copy -a "src=/etc/hosts dest=/tmp mode=0644 backup=yes"

# src 本地文件或目录
# src 若为 /etc 则代表把 /etc 目录复制到远端
# src 若为 /etc/ 则代表把 /etc/ 目录下的文件复制到远端
# dest 远端文件或目录
# mode 指定复制过去文件或目录的权限，可使用所有的权限格式
# backup 指定为yes时目标主机上已存在文件时在覆盖前对源文件进行备份
# 默认不备份直接覆盖
```

#### script 模块

```
ansible web_server -m script -a "/server/scripts/test.sh"

# 在远端服务器上执行本地的Shell脚本
# 若脚本指定的参数为文件，则文件需要存在于远端服务器上
```

#### cron 模块

```
ansible web_server --private-key ~/.ssh/id_cluster -m cron -a "name='backup data' minute=5 hour=3 job='/server/scripts/backup.sh > /dev/null 2>&1'"

# name 指定任务的名称（注释信息）
# minute    0-59    *    */2    etc
# hour      0-23    *    */2    etc
# day       1-31    *    */2    etc
# month     1-12    *    */2    etc
# weekday   0-6     *    */2    etc
# job       The command to execute

# state=absent 删除定时任务
# backup=yes 备份

# 删除示例
ansible web_server --private-key ~/.ssh/id_cluster -m cron -a "name='backup data' state=absent backup=yes"

# name 不变，再次执行命令则会覆盖原name命令，可以理解为修改
```



### 剧本

> YAML文件开头需要先写三个减号---，多个分组信息需要间隔一致才能执行，上下也要对齐，后缀名一般为.yml
>
> Playbook剧本文件的结构由四部分组成——target、variable、task、handler。target部分用于定义要执行剧本的主机节点范围、variable部分用于定义剧本执行时要用的变量、task部分用于定义将在远程主机上执行的任务列表、handler部分用于定义执行完成后需要调用的后续任务。
>
> 其中name字段代表此项Play（动作）的名字，可自行命名，没有限制，用于在执行过程中提示用户执行到了那一步，以及帮助管理员日后阅读时回忆起这段代码的作用。
>
> hosts字段代表要在哪些主机节点上执行该剧本，多个主机组之间用逗号间隔，若需要对全部主机进行操作则用all参数。
>
> tasks字段用于定义要执行的任务，每个任务都要有一个独立的name字段进行命名，并且每个任务的name字段和模块名称都要严格上下对齐，参数要单独缩进。

#### 编写剧本

```bash
cat > /opt/packages.yml << EOF
---
- name: 安装软件包
  hosts: balancers
  tasks:
    - name: install mariadb
      yum:
        name: mariadb
        state: latest
EOF
```

#### 执行剧本

```bash
ansible-playbook /opt/packages.yml
```
