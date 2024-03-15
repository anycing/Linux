# 批量创建特殊要求用户

### 需求：

> 批量创建10个系统账号 will01 - will10 并设置密码（密码为随机数，数字和字符混合）



### 解决方案：

#### 数组方式

```
#!/bin/bash

. /etc/init.d/functions

user=will
passwdFile='/tmp/passAuth.txt'

users=(`echo ${user}{01..10}`)

for user in ${users[@]}
do
    useradd $user &> /dev/null

    if [ $? -eq 0 ]; then
        action "$user added successfully" /bin/true
        password=`openssl rand -base64 8`

        echo "$password" | passwd --stdin $user &> /dev/null

        if [ $? -eq 0 ]; then
            action "$user password updated successfully"
            echo "$user:$password" >> $passwdFile
        else
            action "$user password updated failed"
        fi

    else
        action "$user add failed" /bin/false

    fi
done
```

#### 非数组方式

```
#!/bin/bash

. /etc/init.d/functions

user=will
passwdFile='/tmp/passAuth.txt'

for i in `seq -s " " -w 10`
do
    useradd $user$i &> /dev/null

    if [ $? -eq 0 ]; then
        action "$user$i added successfully" /bin/true
        password=`openssl rand -base64 8`

        echo "$password" | passwd --stdin $user$i &> /dev/null

        if [ $? -eq 0 ]; then
            action "$user$i password updated successfully"
            echo "$user$i:$password" >> $passwdFile
        else
            action "$user$i password updated failed"
        fi

    else
        action "$user$i add failed" /bin/false

    fi
done
```

### 拓展：

#### 输出00-10

```
echo {00..10}
```

```
seq -s " " -w 10
```

