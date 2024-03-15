# judge service

### 本地服务及端口监控

```
netstat -lntup | grep nginx | wc -l
```

```
ss -lntup | grep nginx | wc -l
```

```
lsof -i :80 | wc -l
```

### 远程端口监控

```
nmap -p 80 127.0.0.1 | grep open | wc -l
```

```
echo -e "\n" | telnet 127.0.0.1 80 2> /dev/null | grep Connected | wc -l
```

```
nc -z 127.0.0.1 80

echo $?
```

### 客户端模拟监控

```
wget -q 127.0.0.1 &> /dev/null

echo $?
```

```
curl 127.0.0.1 &> /dev/null

echo $?
```
