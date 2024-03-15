# Sersync

{% hint style="warning" %}
**Sersync 服务需要在Rsync基础上搭建，以下操作在客户端上完成**
{% endhint %}

### 确保 Rsync 可推送成功

```
rsync -az /etc/hosts rsync_backup@172.16.1.41::backup
```



### 安装 Sersync

```
cd /server/tools


wget https://raw.githubusercontent.com/wsgzao/sersync/master/sersync2.5.4_64bit_binary_stable_final.tar.gz

# 备用地址：
# https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/sersync/sersync2.5.4_64bit_binary_stable_final.tar.gz

tar xf sersync2.5.4_64bit_binary_stable_final.tar.gz


mkdir -p /application/sersync

mkdir -p /application/sersync/bin

mkdir -p /application/sersync/conf

mkdir -p /application/sersync/logs


cp ./GNU-Linux-x86/sersync2 /application/sersync/bin

cp ./GNU-Linux-x86/confxml.xml /application/sersync/conf
```



### 配置文件说明

```
cat /application/sersync/conf/confxml.xml

<?xml version="1.0" encoding="ISO-8859-1"?>
<head version="2.5">
# 主机
    <host hostip="localhost" port="8008"></host>
    <debug start="false"/>
    <fileSystem xfs="false"/>
    
# 过滤
    <filter start="false">
        <exclude expression="(.*)\.svn"></exclude>
        <exclude expression="(.*)\.gz"></exclude>
        <exclude expression="^info/*"></exclude>
        <exclude expression="^static/*"></exclude>
    </filter>
    
# inotify 监控的事件
    <inotify>
        <delete start="true"/>
        <createFolder start="true"/>
        <createFile start="false"/>
        <closeWrite start="true"/>
        <moveFrom start="true"/>
        <moveTo start="true"/>
        <attrib start="false"/>
        <modify start="false"/>
    </inotify>

    <sersync>
# 要监控的目录
        <localpath watch="/opt/tongbu">
# rsync 服务器的配置参数
            <remote ip="172.16.1.41" name="/data"/>
            <!--<remote ip="192.168.8.39" name="tongbu"/>-->
            <!--<remote ip="192.168.8.40" name="tongbu"/>-->
        </localpath>
        <rsync>
# rsync 传输时的参数
            <commonParams params="-artuz"/>
            # auth start 参数改为 true
            <auth start="true" users="rsync_backup" passwordfile="/etc/rsync.password"/>
            <userDefinedPort start="false" port="874"/><!-- port=874 -->
            <timeout start="false" time="100"/><!-- timeout=100 -->
            <ssh start="false"/>
        </rsync>
        <failLog path="/tmp/rsync_fail_log.sh" timeToExecute="60"/><!--default every 60mins execute once-->
        <crontab start="false" schedule="600"><!--600mins-->
            <crontabfilter start="false">
                <exclude expression="*.php"></exclude>
                <exclude expression="info/*"></exclude>
            </crontabfilter>
        </crontab>
        <plugin start="false" name="command"/>
    </sersync>

    <plugin name="command">
        <param prefix="/bin/sh" suffix="" ignoreError="true"/>  <!--prefix /opt/tongbu/mmm.sh suffix-->
        <filter start="false">
            <include expression="(.*)\.php"/>
            <include expression="(.*)\.sh"/>
        </filter>
    </plugin>

    <plugin name="socket">
        <localpath watch="/opt/tongbu">
            <deshost ip="192.168.138.20" port="8009"/>
        </localpath>
    </plugin>
    <plugin name="refreshCDN">
        <localpath watch="/data0/htdocs/cms.xoyo.com/site/">
            <cdninfo domainname="ccms.chinacache.com" port="80" username="xxxx" passwd="xxxx"/>
            <sendurl base="http://pic.xoyo.com/cms"/>
            <regexurl regex="false" match="cms.xoyo.com/site([/a-zA-Z0-9]*).xoyo.com/images"/>
        </localpath>
    </plugin>
</head>
```



### 启动服务

```
# 启动服务

/application/sersync/bin/sersync2 -d -o /application/sersync/conf/confxml.xml

# -d 使用守护进程模式
# -o 指定配置文件

# pkill sersync 可关闭进程


# 写入开机自启动

vim /etc/rc.d/rc.local

/application/sersync/bin/sersync2 -d -o /application/sersync/conf/confxml.xml

```

> 由于不是yum安装，所以不能systemctl启动，可以自己手动写成服务，然后就可以使用systemctl进行管理了
