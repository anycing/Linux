# File & Directory Management

### 文件与目录查看命令

#### ls

```
ls


# NAME: list directory contents

# -l （小写L）显示文件的详细信息
# -h 人类可读
# -a 显示所有文件
# -A 显示除了.与..外的所有文件
# -r 逆序显示
# -t 按时间排序
# -R 递归显示
```



### 路径操作（分绝对路径和相对路径）

#### pwd

```
pwd

# 显示当前路径


# NAME: print name of current/working directory
```

#### cd

```
cd DIRECTORY

# 进入指定的DIRECTORY路径


# NAME: change the working directory

# - 返回上一次访问目录
# .. 返回上级目录
# ~ 返回用户家目录（可省略，直接cd）
```



### 建立与删除目录

#### mkdir

```
mkdir DIRECTORY

# 新建DIRECTORY目录


# NAME: make directories

# -p Create any missing intermediate pathname components.
```

#### rmdir

```
rmdir DIRECTORY

# 删除DIRECTORY目录（DIRECTORY必须为空）


# NAME: remove empty directories
```



### 复制文件或目录

#### cp

```
cp SOURCE_FILE TARGET_FILE

# Copy SOURCE_FILE to TARGET_FILE


cp SOURCE_FILE TARGET_DIRECTORY

# Copy multiple SOURCE_FILE(s) to TARGET_DIRECTORY.


# NAME: copy files and directories

# -r -R 递归复制，用于复制目录
# -p 保留副本每个源文件的用户权限、时间相关属性
# -d 保留链接
# -a 尽可能保留源文件的属性，等同于-dpr
# -v 可视化，显示做了的操作
```



### 删除文件或目录

#### rm

```
rm FILE


# NAME: remove files or directories

# -r -R 递归删除
# -f 强制删除不提示
```



### 移动与重命名

#### mv

```
mv SOURCE_FILE TARGET_FILE

# 重命名文件


mv SOURCE_FILE TARGET_DIRECTORY

# 移动文件


# NAME: move (rename) files

# -t TARGET_DIRECTORY，相当于先写目标路径
# -v 可视化，显示做了的操作
```



### 通配符

| Character         | Description      |
| ----------------- | ---------------- |
| \*                | 匹配任意字符           |
| ?                 | 匹配单个字符           |
| \[xyz]            | 匹配 xyz 任意一个字符    |
| \[a-z]            | 匹配字符范围           |
| \[!xyz] 或 \[^xyz] | 匹配不存在 zyx 中的任意字符 |
