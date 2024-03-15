# Project2

## NFS项目实践2

> NFS 共享的匿名用户用 www ，使得客户端上传图片都是 www 用户，而不是匿名 nfsnobody
>
> web01 web02 客户端实现挂载到nfs01
>
> NFS 下面共享 /data ，允许 web01 web02 客户端（挂载点 /mnt/data ）挂载后可读
>
> web01 上传图片，web02 服务上可以删除 web01 上传的图片
>
> 实现开机自动挂载（使用优化的参数挂载）



### nfs01

```
yum install nfs-utils rpcbind -y


systemctl start rpcbind

systemctl start nfs

# 先启动rpcbind，顺序不能反


systemctl enable rpcbind

systemctl enable nfs
```

```
useradd -u 1111 www

id www
```

```
vim /etc/exports

/data 172.16.1.0/24(rw,sync,all_squash,anonuid=1111,anongid=1111)

# all_squash
# 客户端所有用户都使用匿名用户
# anonuid=1111,anongid=1111
# 并且此匿名用户的uid为1111
```

```
mkdir -p /data

mkdir -p /data1


chown -R www.www /data

chown -R www.www /data1
```

```
systemctl reload nfs


exportfs -r

# 两条等价二选一


showmount -e 172.16.1.31
```

###

### web01

```
useradd -u 1111 www
```

```
mkdir -p /mnt/data

mkdir -p /mnt/data1


# 实现开机自动挂载方式一：

vim /etc/rc.d/rc.local

/usr/bin/mount -t nfs 172.16.1.31:/data /mnt/data


chmod u+x /etc/rc.d/rc.local

# 需要给执行权限，否则无效


reboot
```



### web02

```
useradd -u 1111 www
```

```
mkdir -p /mnt/data

mkdir -p /mnt/data1


# 实现开机自动挂载方式二：

vim /etc/fstab

172.16.1.31:/data       /mnt/data               nfs     defaults,_netdev        0 0


# 文件系统先于网络启动，需要依赖远程文件系统服务才可完成挂载

systemctl restart remote-fs.target

systemctl enable remote-fs.target


reboot
```
