# Runlevel

### Overview: CentOS 6 & 7 runlevel

| CentOS 6 | CentOS 7          | Function               |
| -------- | ----------------- | ---------------------- |
| 0        | poweroff.target   | halt                   |
| 1        | rescue.target     | Single user mode       |
| 2        | multi-user.target | Multiuser, without NFS |
| 3        | multi-user.target | Full multiuser mode    |
| 4        | multi-user.target | unused                 |
| 5        | graphical.target  | X11                    |
| 6        | reboot.target     | reboot                 |

###

### View current runlevel

#### CentOS 6 & 7

```
runlevel
```

```
who -r
```



### Switch current runlevel

```
init <runlevel code>
```



### View system default runlevel

#### CentOS 6

```
cat /etc/inittab
```

#### CentOS 7

```
systemctl get-default
```



### Set system default runlevel

#### CentOS 6

```
vim /etc/inittab
```

#### CentOS 7

```
systemctl set-default TARGET.target
```

