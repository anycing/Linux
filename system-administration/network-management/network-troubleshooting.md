# Network Troubleshooting

### 网络故障排除常用命令

* ping
* traceroute
* mtr
* nslookup
* telnet
* tcpdump
* netstat
* ss

前4个用于检测到目标主机之间的连接问题，后4个用于排查主机上更细致的问题。



### 命令解释

| Command    | Description                                                                                                          |
| ---------- | -------------------------------------------------------------------------------------------------------------------- |
| ping       | send ICMP ECHO\_REQUEST to network hosts                                                                             |
| traceroute | print the route packets trace to network host                                                                        |
| mtr        | a network diagnostic tool                                                                                            |
| nslookup   | query Internet name servers interactively                                                                            |
| telnet     | user interface to the TELNET protocol                                                                                |
| tcpdump    | dump traffic on a network                                                                                            |
| netstat    | print network connections, routing tables, interface statistics, masquerade con- nections, and multicast memberships |
| ss         | another utility to investigate sockets                                                                               |



| Command    | Function                           |
| ---------- | ---------------------------------- |
| ping       | 检测当前主机与目标主机是否畅通（不通时除了网络中断还可能存在防火墙） |
| traceroute | 检测网络质量，追踪到服务器的每一跳（ping通但还是异常）      |
| mtr        | 检测到目标主机之间是否有数据包丢失（ping通但还是异常）      |
| nslookup   | 域名方式访问时显示域名对应的ip                   |
| telnet     | 检测端口的连接状态                          |
| tcpdump    | 分析数据包                              |
| netstat    |                                    |
| ss         |                                    |



### 命令演示

```
ping www.baidu.com
```

```
traceroute -w 1 www.baidu.com

# -w 超时等待时间，此处设置为 1.0 sec (default 5.0 sec)
```

```
mtr

# mtr比traceroute 显示内容丰富，建议使用mtr
```

```
telnet www.baidu.com 80

# 退出使用 control + ] ，然后输入quit
```

```
tcpdump -i any -n port 80

# -i 指定网卡，any 所有网卡
# -n 不显示域名，以ip形式显示
# -w 保持到文件
# host 指定主机
# port 指定端口

# e.g.
#    tcpdump -i any -n host x.x.x.x
#    tcpdump -i any -n host x.x.x.x and port 80
#    tcpdump -i any -n host x.x.x.x and port 80 -w /DIR/FILE
```

```
netstat -ntpl

# -n 不显示域名，以ip形式显示
# -t 只显示tcp协议的内容
# -p Show the PID and name of the program to which each socket belongs.
# -l Show only listening sockets.
```

```
ss -ntpl

# 同 netstat
```

