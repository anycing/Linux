# Partition

### What is partition ?

磁盘分区相当于给磁盘打隔断（修改64字节的分区表DPT的相关信息）

磁盘最多有4个主分区（可包含1个扩展分区，逻辑分区都建立在扩展分区之上）



### 磁盘和分区在Linux中的命名？

#### IDE

| Disk name | Partition name                      |
| --------- | ----------------------------------- |
| /dev/hda  | /dev/hda1    /dev/hda2    /dev/hda3 |
| /dev/hdb  | /dev/hdb1    /dev/hdb2    /dev/hdb3 |
| /dev/hdc  | /dev/hdc1    /dev/hdc2    /dev/hdc3 |

#### SCSI

| Disk name | Partition name                      |
| --------- | ----------------------------------- |
| /dev/sda  | /dev/sda1    /dev/sda2    /dev/sda3 |
| /dev/sdb  | /dev/sdb1    /dev/sdb2    /dev/sdb3 |
| /dev/sdc  | /dev/sdc1    /dev/sdc2    /dev/sdc3 |



### 实战分区

#### MBR 分区方案（Master Boot Record 主引导记录分区方案适用于小于2T的磁盘分区）

```
fdisk DISK

# -l 查看分区信息
# -l 后不加 DISK 可直接看所有磁盘的分区信息


# NAME: manipulate disk partition table
```

```
fdisk /dev/DISK

m

# d   delete a partition
# n   add a new partition
# p   print the partition table
# q   quit without saving changes
# w   write table to disk and exit（***保持并退出***）

n

p

return    # (Partition number, default)

return    # (First sector, default)

+150M     #（单位可以是：K, M, G）

p

w


# 手动添加了一个150M的主分区
```

```
partprobe /dev/DISK

# inform the OS of partition table changes
```

```
   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048      309247      153600   83  Linux
/dev/sdb2          309248      616447      153600   83  Linux
/dev/sdb3          616448      923647      153600   83  Linux
/dev/sdb4          923648     2097151      586752    5  Extended
/dev/sdb5          925696     1232895      153600   83  Linux
/dev/sdb6         1234944     1542143      153600   83  Linux
/dev/sdb7         1544192     2097151      276480   83  Linux


# 将1G的磁盘划分为6个分区（3个主分区，3个逻辑分区）
# ls -l /dev/sd*
```

```
fdisk xxx

# 一条命令完成上述分区
```

#### GPT 分区方案（大于2T的磁盘分区）

parted, gpt 等工具
