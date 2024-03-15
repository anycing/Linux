# Project1

## NFS项目实践

> web01 web02 客户端实现挂载到nfs01
>
> NFS 下面共享 /backup ，允许 web01 web02 客户端（挂载点 /mnt/data ）挂载后可读
>
> web01 上传图片，backup服务上可以删除 web01 上传的图片
>
> NFS 下面共享 /data1 ，允许 web01 web02 客户端192.168.1.0/24 网段只读（ data1 ）
>
> 实现开机自动挂载



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
vim /etc/exports

/data 172.16.1.0/24(rw,sync)
/data1 192.168.1.0/24(ro)
```

```
mkdir -p /data

mkdir -p /data1


grep nfs /etc/passwd


chown -R nfsnobody.nfsnobody /data

chown -R nfsnobody.nfsnobody /data1
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
mkdir -p /mnt/data

mkdir -p /mnt/data1


# 实现开机自动挂载方式一：

vim /etc/rc.d/rc.local

/usr/bin/mount -t nfs 172.16.1.31:/data /mnt/data
/usr/bin/mount -t nfs 192.168.1.31:/data1 /mnt/data1


chmod u+x /etc/rc.d/rc.local

# 需要给执行权限，否则无效


reboot
```



### web02

```
mkdir -p /mnt/data

mkdir -p /mnt/data1


# 实现开机自动挂载方式二：

vim /etc/fstab

172.16.1.31:/data       /mnt/data               nfs     defaults,_netdev        0 0
192.168.1.31:/data1     /mnt/data1              nfs     defaults,_netdev        0 0


# 文件系统先于网络启动，需要依赖远程文件系统服务才可完成挂载

systemctl restart remote-fs.target

systemctl enable remote-fs.target


reboot
```
