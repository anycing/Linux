# Network Management

### 常用网络工具包

| net-tools | iproute2 |
| --------- | -------- |
| ifconfig  | ip       |
| route     | ss       |
| netstat   |          |



### 常用网卡命令

#### 查看网卡的物理连接状态

```
mii-tool eth0

# 查看eth0网卡的网线连接情况


# NAME: view, manipulate media-independent interface status
```

#### 插拔网卡

```
ifconfig eth0 up

ifconfig eth0 down
```

#### 启动&关闭网卡

```
ifup eth0

ifdown eth0
```

#### 查看网卡信息(ip netmask route)

```
ifconfig

ifconfig eth0


# NAME: configure a network interface
```

#### 设置ip与子网掩码

```
ifconfig eth0 192.168.x.x netmask 255.255.255.0

# CentOS 7 设置后默认网关可能会丢失，丢失后手动添加网关即可
# 编辑/etc/sysconfig/network-scripts/下的网卡文件可进行更详细的配置
```



### 常用路由命令

#### 查看网关/路由信息

```
route

# -n 不解析主机名


# NAME: show / manipulate the IP routing table
```

#### 添加默认网关

```
route add default gw x.x.x.x
```

#### 删除默认网关

```
route del default gw x.x.x.x
```

#### 添加明细路由

```
route add -host x.x.x.x gw x.x.x.x

# 根据访问指定主机添加路由
```

```
route add -net x.x.x.0 netmask x.x.x.x gw x.x.x.x

# 根据访问指定网段添加路由（网段地址一般最后一位为0）
```

#### 删除明细路由

```
route del -host x.x.x.x
```

```
route del -net x.x.x.0 netmask x.x.x.x
```

