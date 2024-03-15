# Rsync

### Name & Common Options

```
rsync - a fast, versatile, remote (and local) file-copying tool

# -a 包含 -rtopDlg
# -v --verbose 可视
# -P 显示进度

# -z --compress 压缩
# -r --recursive 递归

# -t --times preserve modification times
# -o --owner preserve owner (super-user only)
# -p --perms preserve permissions
# -g --group preserve group

# -D same as --devices --specials preserve device files (super-user only)
# -l --links 复制符号链接

# --partial 断点续传
# --bwlimit=RATE limit socket I/O bandwidth 限速，单位：KB/S

# --delete delete extraneous files from dest dirs 删除那些接收端还有而发送端已经不存在的文件
# --exclude 排除一个不需要传输的文件
# --exclude-from=FILE 从 FILE 中读取排除规则

# -e 指定替代 rsh 的 shell 程序
```



### Tips:

{% hint style="danger" %}
使用sync过程中null与null/的区别：

**null** 是目录本身和目录下的内容

**null/** 只是目录下的内容
{% endhint %}



```
For more help information:

man rsyncd.conf

or go to
https://www.samba.org/ftp/rsync/rsync.html
```
