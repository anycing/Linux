# View System Informations

### View Kernel information

```
uname -r
```

### View File System information

```
df -h
```

```
df -i
```

### View File System Attributes

```
blkid
```

### View Memory information

```
free -m
```

```
cat /proc/meminfo
```

### View CPU information

```
cat /proc/cpuinfo
```

### View Load information

```
uptime

# output result mean:
# Col 1 : The current time
# Col 2 : how long the system has been running
# Col 3 : how many users are currently logged on
# Col 4-6 : the system load averages for the past 1, 5, and 15 minutes.
```

```
cat /proc/loadavg
```

### View current device mount list information

```
cat /proc/mounts
```

### View current system interrupt information

```
cat /proc/interrupts
```

### View service name by port number

```
lsof -i :PORT
```

```
netstat -lntup | grep PORT
```

