# System Settings

### MAC OS Connect Warning

```
ssh USER@HOST

# -bash: warning: setlocale: 
# LC_CTYPE: cannot change locale (UTF-8): No such file or directory

locale

# LANG=en_US.UTF-8
# LC_CTYPE=UTF-8
# These two options are different, they should be same.

vim /etc/locale.conf

# LANG="en_US.UTF-8"
# append: LC_CTYPE="en_US.UTF-8"

exit
```



### Modify the name of the network card to " eth0 "

```
cd /etc/sysconfig/network-scripts/

mv ifcfg-ens33 ifcfg-eth0

vim ifcfg-eth0
# modify: NAME=eth0
# modify: DEVICE=eth0
```

```
vim /etc/default/grub

# GRUB_CMDLINE_LINUX append: biosdevname=0 net.ifnames=0
```

```
grub2-mkconfig -o /boot/grub2/grub.cfg
```

```
systemctl reboot
```



### Hostname setting

#### CentOS 6&#x20;

```
vim /etc/sysconfig/network

# HOSTNAME=<HOSTNAME>
```

```
vim /etc/hosts

# hosts文件中添加：127.0.0.1   <HOSTNAME>
```

```
hostname <HOSTNAME>
```

```
exec bash
```

#### CentOS 7

```
hostnamectl set-hostname <HOSTNAME>
```

```
vim /etc/hosts

# hosts文件中添加：127.0.0.1   <HOSTNAME>
```

```
exec bash
```



### System Time setting&#x20;

```
date -s "2000/11/1 00:00:00"
```

```
clock -w

# Set the Hardware Clock to the current System Time.
```



### Filesystem mount or unmount

#### Mount a filesystem

```
mount /dev/cdrom /mnt
```

#### Unmount a filesystem

```
umount /mnt
```

#### Config system startup auto mount

```
vim /etc/fstab
```



### SELinux setting

#### View SELinux state

```
getenforce
```

#### Open SELinux temporarily

```
setenforce 1
```

#### Close SELinux temporarily

```
setenforce 0
```

#### Set SELinux state permanently

```
vim /etc/selinux/config
```

###

### Firewall setting

#### CentOS 6 - iptables

```
service iptables status
```

```
service iptables start
service iptables stop
service iptables restart
```

```
/etc/init.d/iptables start
/etc/init.d/iptables stop
/etc/init.d/iptables restart
```

```
chkconfig iptables on
chkconfig iptables off
```

#### CentOS 7 - firewalld

```
systemctl status firewalld.service
```

```
systemctl start firewalld.service
systemctl stop firewalld.service
systemctl restart firewalld.service
```

```
systemctl enable firewalld.service
systemctl disable firewalld.service
```
