# Access via remote shell

### pull

```
rsync [OPTION...] [USER@]HOST:SRC... [DEST]

e.g.:

rsync -avz root@172.16.1.41:/etc/hosts /tmp/

rsync -avz  -e "ssh -p 22" root@172.16.1.41:/etc/hosts /tmp/

# 上述两条命令等价，第一条为第二条的默认省略形式
# 拉远程服务器的/etc/hosts文件至本地/tmp/目录下
```



### push

```
rsync [OPTION...] SRC... [USER@]HOST:DEST

e.g.:

rsync -avz /etc/hosts root@172.16.1.41:/tmp/

rsync -avz /etc/hosts -e "ssh -p 22" root@172.16.1.41:/tmp/

# 上述两条命令等价，第一条为第二条的默认省略形式
# 推本地/etc/hosts文件至远程服务器的/tmp/目录下
```

