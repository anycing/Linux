# User Management

### 添加用户

```
useradd USERNAME

# -d 指定家目录
# -g 指定属组 e.g. useradd -g GROUP USERNAME
# -G 指定附加组
# -s 指定shell

# NAME: create a new user or update default new user information
```

### 查看用户ID

```
id USERNAME


# NAME: print real and effective user and group IDs
```

### 修改密码

```
passwd USERNAME

# 修改当前用户的密码时USERNAME可缺省


# NAME: update user's authentication tokens
```

### 删除用户

```
userdel USERNAME

# -r 删除家目录和相关邮件目录


# NAME: delete a user account and related files
```

### 修改用户账户

```
usermod USERNAME

# -d 修改用户家目录
# -g 修改用户属组 e.g. usermod -g GROUP USERNAME
# -G 修改附加组
# -a -G 追加附加组


# NAME: modify a user account
```

### 修改密码过期时间

```
chage USERNAME


# NAME: change user password expiry information
```

### 新建用户组

```
groupadd GROUP


# NAME: create a new group
```

### 删除用户组

```
groupdel GROUP


# NAME: delete a group
```

### 切换用户

```
su USERNAME

# - 切换用户的同时切换用户家目录 e.g. su - USERNAME


# NAME: run a command with substitute user and group ID
```

### 以其它用户身份执行命令

```
sudo COMMAND


# NAME: execute a command as another user
```

```
visudo

# USERNAME ALL=(ALL) NOPASSWD: /usr/sbin/shutdown -c
# 配置USERNAME无需密码执行 shutdown -c 命令

# NAME: edit the sudoers file, sudoers allows particular users to run various commands as the root user
```

```
vim /etc/sudoers

# 同上
```

### /etc/passwd

```
cat /etc/passwd

# root:x:0:0:root:/root:/bin/bash
# bin:x:1:1:bin:/bin:/sbin/nologin

# 七个字段依次为：
# 1: username
# 2: password
# 3: uid
# 4: gid
# 5: comment
# 6: home directory
# 7: shell

# 删除第二个字段则登录时无需密码
# 修改最后一个字段为/sbin/nologin则用户无法登陆
```
