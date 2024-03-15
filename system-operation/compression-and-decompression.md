# Compression and Decompression

### Compression

#### tar

```
tar -cf archive.tar /etc


# NAME: an archiving utility

# -c 创建
# -v 可视化
# -p 还原时保留文件属性
# -h 备份链接指向的源文件而不是链接本身
# -f 指定文件名（-f后边必须紧跟文件名）
```

```
tar -czf archive.tar.gz /etc

# -z 使用gzip压缩
# -J 使用xz压缩
# -j 使用bz2压缩
```

#### zip

```
zip -rqo -9 archive.zip /etc

# -rqo 递归 安静 输出文件 -9 压缩级别：1-9
# 将/etc全部打包为archive.zip
```



### Decompression

#### tar

```
tar -xf archive.tar -C /etc

# -x 解包
# -C 指定路径的已存在目录
```

```
tar -xzf archive.tar.gz

# -xz 解压gz包 archive.tar.gz
```

```
tar -tf archive.tar

# -t 只查看不解包
```

#### unzip

```
unzip -q archive.zip -d /etc/dir

# 安静模式解压文件到指定路径

# -q 安静模式
# -d 指定路径
```

```
unzip -O GBK 中文压缩包.zip

# -O 以指定编码解压
```

```
unzip -l archive.zip

# -l 只查看内容，不解压
```

