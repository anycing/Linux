# Format & Mount

### Format

```
mkfs -t ext4 /dev/sdb1

# -t Specify the type of filesystem to be built. 指定文件系统类型
# 常见文件系统类型：
#     ext3 (C5)
#     ext4 (C6)
#     xfs (C7)


# NAME: build a Linux filesystem
```

更多格式化命令可以使用 `mk + tab`  查看相关命令配合man使用



### Mount

```
mount -t ext4 /dev/sdb1 /mnt


# NAME: mount a filesystem
```

#### 开机自动挂载

```
vim /etc/fstab

# 可先用blkid查看相关文件系统属性，再编辑fstab
```

#### 查看挂载结果

```
df -h
```

```
cat /proc/mounts

# 查看挂载结果（df -h 无法查看时的另一种方法）
```

