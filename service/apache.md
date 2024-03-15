# Apache

## 编译安装Apache



### 下载源代码

```
wget https://mirrors.aliyun.com/apache/httpd/httpd-2.4.41.tar.gz


# 此为阿里云地址
# 阿里云提供的Apache地址：https://mirrors.aliyun.com/apache/httpd/

# 官网地址：
# http://httpd.apache.org/
# 此版本官网下载地址为：
# https://www-us.apache.org/dist//httpd/httpd-2.4.41.tar.gz
```

```
wget https://mirrors.aliyun.com/apache/apr/apr-1.7.0.tar.gz


# 此为阿里云地址
# 阿里云提供的apr地址：https://mirrors.aliyun.com/apache/apr/

# 官方地址：
# http://apr.apache.org/download.cgi
# 此版本官网下载地址为：
# https://www-us.apache.org/dist//apr/apr-1.7.0.tar.gz
```

```
wget https://mirrors.aliyun.com/apache/apr/apr-util-1.6.1.tar.gz


# 此为阿里云地址
# 阿里云提供的apr地址：https://mirrors.aliyun.com/apache/apr/

# 官方地址：
# http://apr.apache.org/download.cgi
# 此版本官网下载地址为：
# https://www-us.apache.org/dist//apr/apr-util-1.6.1.tar.gz
```

```
wget https://ftp.pcre.org/pub/pcre/pcre-8.43.tar.gz
```

```
tar -xzvf httpd-2.4.41.tar.gz

tar -xzvf apr-1.7.0.tar.gz

tar -xzvf apr-util-1.6.1.tar.gz

tar -xzvf pcre2-10.34.tar.gz
```



### 安装编译器和所需开发包

```
yum install gcc gcc-c++ expat-devel libxml2-devel -y

# 当编译安装提示PCRE需要使用dev安装时，可安装 pcre-devel 开发包
```



### 安装apr

```
cd apr-1.7.0


./configure --prefix=/usr/local/apr/

make -j2

make install
```



### 安装apr-util

```
cd apr-util-1.6.1


./configure --prefix=/usr/local/apr-util/ --with-apr=/usr/local/apr/

make -j2

make install
```



### 安装pcre

```
cd pcre-8.43

./configure --prefix=/usr/local/pcre/

make -j2

make install
```



### 安装httpd

```
cd httpd-2.4.41


./configure --prefix=/usr/local/httpd/ --with-apr=/usr/local/apr/ --with-apr-util=/usr/local/apr-util/ --with-pcre=/usr/local/pcre/

make -j2

make install
```

