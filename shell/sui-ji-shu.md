# 随机数

### openssl

```
openssl rand -base64 32 | sed "s#[^a-z0-9]##g" | cut -c 1-10

# 使用OpenSSL的rand命令产生32位随机数并使用base64编码输出
# 只保留小写字母和数字
# 截取前10位显示输出

# -hex 以16进制编码输出
```

```
# 可对结果利用管道符进行二次处理：
# | openssl md5
# | openssl sha256

openssl rand -base64 64 | openssl sha256 | awk '{print $NF}' | tr [a-z] [0-9]
```

### $RANDOM

```
echo $RANDOM

# 范围：0-32767
```

### date

```
date "+%N"

# 纳秒 9位
```

### /dev/urandom

```
head /dev/urandom | cksum | awk '{print $1}'
```

### uuid

```
cat /proc/sys/kernel/random/uuid
```



### 生成32位随机数常用方式

```
head /dev/urandom | openssl md5 | awk '{print $NF}' | tr [a-f] [0-5]
```
