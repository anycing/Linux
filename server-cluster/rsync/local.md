# Local

### copy file

```
rsync -avz /etc/hosts /tmp

rsync -zrtopg /etc/hosts /tmp

# 工作中rsync的两种常用选项，复制hosts到/tmp目录
```



### delete directory

```
​mkdir /null

rsync -r --delete /null/ /tmp

# 使用rsync清空/tmp目录
```



### delete file content

```
touch /null.txt

rsync -r --delete /null.txt /etc/hosts

# 使用rsync命令清空hosts文件
```



### list file & directory attributes

```
rsync FILE
```

