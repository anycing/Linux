# Dynamic & Static IP Configuration

#### Edit Network Card  Configuration

```
cd /etc/sysconfig/network-scripts/

vim ifcfg-eth0
```

### Dynamic IP Configuration

```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth0
UUID=9c12756b-085d-4a34-9909-650276ac733c
DEVICE=eth0
ONBOOT=yes
```

### Static IP Configuration

```
TYPE=Ethernet
BOOTPROTO=none
NAME=eth0
UUID=9c12756b-085d-4a34-9909-650276ac733c
DEVICE=eth0
ONBOOT=yes
IPADDR=192.168.125.200
NETMASK=255.255.255.0
GATEWAY=192.168.125.2
DNS1=114.114.114.114
DNS2=8.8.8.8
```

```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth0
UUID=9c12756b-085d-4a34-9909-650276ac733c
DEVICE=eth0
ONBOOT=yes
IPADDR=10.0.0.200
PREFIX=24
GATEWAY=10.0.0.254
DNS1=223.5.5.5

# 此为使用nmtui设置静态ip GATEWAY DNS1 同时将ONBOOT设置为了yes后的网卡配置文件内容

# 建议使用nmtui进行手动设置


# 与系统原始配置不同的是（除NAME、UUID、DEVICE外）：
# BOOTPROTO=none
# ONBOOT=yes
# 同时增加了以下项：
# IPADDR=10.0.0.200
# PREFIX=24
# GATEWAY=10.0.0.254
# DNS1=223.5.5.5
```

#### Check

```
service network restart
```

```
systemctl restart NetworkManager.service
```

```
ifconfig eth0

route -n
```
