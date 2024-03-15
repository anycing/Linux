# Project

## 实践秘钥认证

### 环境准备

> m01 172.16.1.61     （放私钥）
>
> web01 172.16.1.17 （放私钥）
>
> web02 172.16.1.18 （放私钥）



### 生成秘钥对

```
ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_cluster -q

# 此命令可不交互生成秘钥对
# -t 指定秘钥类型
# -b 指定长度
# -N 指定秘钥的密码，此示例为空
# -f 指定生成秘钥的位置和文件名
# -q 不输出信息
```

### 分发公钥

```
ssh-copy-id -i ~/.ssh/id_cluster.pub -o StrictHostKeyChecking=no root@172.16.1.17

# -o StrictHostKeyChecking=no
# 刚开始不检查主机key信息

# 也可手动追加公钥信息至远端 ~/.ssh/authorized_keys 文件中
# 手动添加后需要修改文件权限为600，所在文件夹权限为700


# 可在远端查看是否发送成功
# cat ~/.ssh/authorized_keys
```

### 批量分发公钥脚本

```
yum install sshpass -y

# 使用 sshpass 可避免交互式输入密码
```

```
vim /server/scripts/distribute_keys.sh

# 创建批量公钥分发脚本，内容如下
```

```
#!/bin/bash

for ip in 17 18
do
    sshpass -p "centos7" ssh-copy-id -i ~/.ssh/id_cluster.pub -o StrictHostKeyChecking=no root@172.16.1.$ip
done
```

```
chmod 700 /server/scripts/distribute_keys.sh
```

#### 一键实现脚本

```
#!/bin/bash

yum install sshpass -y

ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_cluster -q

for ip in 17 18
do
    sshpass -p "centos7" ssh-copy-id -i ~/.ssh/id_cluster.pub -o StrictHostKeyChecking=no root@172.16.1.$ip
done
```



### 客户端远程连接

```
ssh -i ~/.ssh/id_cluster 172.16.1.17

# -i 指定私钥
```

### 批量执行命令脚本

```
vim /server/scripts/command.sh

#!/bin/bash

for n in 17 18
do
    ssh -i ~/.ssh/cluster root@172.16.1.$n $1
done


chmod u+x /server/scripts/command.sh


# 查看 IP
/server/scripts/command.sh ifconfig
/server/scripts/command.sh "hostname -I"
```

### 批量分发文件脚本

```
vim /server/scripts/distribute.sh

#!/bin/bash

. /etc/init.d/functions

if [ $# -ne 2 ]; then
    echo "usage:$0 localfile remotedir"
    exit 1
fi

for n in 17 18
do
    scp -i ~/.ssh/cluster -P 22 -rp $1 root@172.16.1.$n:$2 &> /dev/null
    if [ $? -eq 0 ]; then
        action "172.16.1.$n succeed" /bin/true
    else
        action "172.16.1.$n fail" /bin/false
    fi
done


chmod u+x /server/scripts/distribute.sh


# 将本地的/etc/hosts文件分发到所有目标服务器的/tmp目录下
/server/scripts/distribute.sh /etc/hosts /tmp
```

