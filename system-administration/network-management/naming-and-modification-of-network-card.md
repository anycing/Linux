# Naming and Modification of Network Card

### CentOS 7 Network Card Naming

CentOS 7 使⽤了一致性⽹络设备命名

| Name   | Description        |
| ------ | ------------------ |
| eno1   | 板载网卡               |
| ens33  | PCI-E⽹卡            |
| enp0s3 | ⽆法获取物理信息的 PCI-E 网卡 |
| eth0   | 以上都不匹配则使⽤ eth0     |



⽹卡命名规则受 `biosdevname` 和 `net.ifnames` 两个参数影响

|     | biosdevname | net.ifnames | ⽹卡名   |
| --- | ----------- | ----------- | ----- |
| 默认  | 0           | 1           | ens33 |
| 组合1 | 1           | 0           | em1   |
| 组合2 | 0           | 0           | eth0  |



### 修改网卡命名为eth0格式

```
cd /etc/sysconfig/network-scripts/

cp ifcfg-ens33 backup_ifcfg-ens33
mv ifcfg-ens33 ifcfg-eth0

vim ifcfg-eth0
# modify: NAME=eth0
# modify: DEVICE=eth0
```

```
vim /etc/default/grub

# GRUB_CMDLINE_LINUX项后面增加 biosdevname=0 net.ifnames=0

# 或者执行：
# grubby --update-kernel=ALL --args="biosdevname=0 net.ifnames=0"
# 执行了这个命令则无效执行下方更新grub的命令
```

```
grub2-mkconfig -o /boot/grub2/grub.cfg

# 更新 grub
```

```
systemctl reboot

# 必须重启，重启后生效

ifconfig
```

