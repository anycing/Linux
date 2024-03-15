# Hostname Settings

### Hostname Settings in CentOS 6&#x20;

```
vim /etc/sysconfig/network

# HOSTNAME=<HOSTNAME>
```

```
vim /etc/hosts

# append：127.0.0.1   <HOSTNAME>
```

```
hostname <HOSTNAME>
```

```
exec bash
```



### Hostname Settings in CentOS 7

```
hostnamectl set-hostname <HOSTNAME>
```

```
vim /etc/hosts

# append：127.0.0.1   <HOSTNAME>
```

```
exec bash
```

