# Authority Management

### 目录权限的表示

```
# x    进入目录
# rx   显示目录内的文件名
# wx   修改目录内的文件名
```



### 修改文件、目录属主

```
chown USER FILE
# 修改FILENAME文件的属主为USER

chown USER:GROUP FILENAME
# 修改FILE的属主为USER，属组为GROUP


# NAME: change file owner and group
```

### 修改文件、目录属组

```
chgrp GROUP FILE
```

```
chown :GROUP FILE
```



### 修改文件、目录权限

```
chmod u+x FILE

# -R 递归修改（当前目录下的所有文件与子目录进行相同的权限变更）

# u 修改属主权限
# g 修改属组权限
# o 修改其它权限
# a 修改所有权限

# + 增加权限    e.g. chmod ug+rw,o-w FILE
# - 减少权限
# = 设置权限    e.g. chomod a=rwx FILE

# 数字修改权限    e.g. chmod 755 FILE


# NAME: change file mode bits
```



### 文件默认权限

文件创建时的默认权限 = 666 - `umask`

#### 查看umask值

```
umask
```



### 锁定文件

```
chattr +i FILE

# +i 锁定文件，不得任意更动文件或目录（无法修改与删除）
# -i 取消锁定
# +a 只可追加内容同时不可删除（常用于日志文件）


# NAME: change file attributes on a Linux file system
```

### 查看文件锁定状态

```
lsattr FILE


# NAME: list file attributes on a Linux second extended file system
```

