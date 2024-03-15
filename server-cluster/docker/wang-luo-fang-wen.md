# 网络访问

### -p / -P 参数说明

| 参数 | 说明   |
| -- | ---- |
| -P | 随机映射 |
| -p | 指定端口 |



### 随机分配映射端口

```
docker pull ghost

docker run -d -P ghost

# 启动ghost博客并随机分配映射端口

# -P 宿主机随机分配映射端口
```

#### 查看端口映射

```
iptables -t nat -L -n
```



### 指定映射端口

```
docker run -d -p 81:80 nginx
```

#### 访问测试

```
curl 192.168.1.61:81
```



### 同主机使用多ip的相同端口提供映射服务

#### 添加多ip

```
ifconfig ens33:1 192.168.1.62/24 up

ifconfig ens33:1
```

#### 指定端口映射

```
docker run -d -p 192.168.1.61:80:80 nginx

docker run -d -p 192.168.1.62:80:2368 ghost
```

#### 测试访问

```
curl 192.168.1.61

curl 192.168.1.62
```



### 同ip使用随机端口映射相同服务

#### 开启服务指定映射

```
docker run -d -p 192.168.1.62::2368 ghost

docker run -d -p 192.168.1.62::2368 ghost

docker run -d -p 192.168.1.62::2368/udp ghost
# 指定udp协议进行映射
```

```
iptables -t nat -L -n
```

#### 访问测试

```
curl 192.168.1.62:上面结果中的随机端口
```



### 综合案例

> 基于Nginx启动一个容器，监听80和81端口，访问80出现nginx默认首页，访问81，出现另外一个站点（站点数据在宿主机上的 /tmp/hello 目录）

```
docker run -d -p 80:80 -p 81:81 -v /tmp/hello.conf:/etc/nginx/conf.d/hello.conf -v /tmp/hello:/tmp nginx:latest
```

```
cat /tmp/hello.conf

server {
    listen       81;
    server_name  localhost;

    location / {
        root   /tmp;
        index  index.html index.htm;
    }
}
```
