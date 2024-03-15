# 常用监控项配置

### 自定义监控项

#### 配置文件中添加监控参数

```bash
vim /etc/zabbix/zabbix_agentd.conf

UserParameter=sda_tps,iostat | awk '/sda/{print $2}'

# sda_tps 为键值
# , 后面为Shell命令
```

#### 命令行取值

```bash
# yum install zabbix-get -y
# Server端安装
rpm -ivh https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-get-4.0.30-1.el7.x86_64.rpm

zabbix_get -s 127.0.0.1 -k sda_tps
# -s 指定agent的IP地址
# -k 指定键值
```

#### 图形端配置

* 信息类型 需选对取值类型
* 应用集建议选上对应的



### 自定义触发器

#### 创建触发器

> 配置 - 主机 - 触发器 - 创建触发器

```
{10.0.0.51:system.users.num.last()}>=2

# 触发器格式
# { hostname : key_name . function() ><= number }
```



### 邮件报警

#### 设置邮件发件人

> 管理 - 报警媒介类型 - Email

#### 设置邮件收件人

> 👤 - 报警媒介 - 添加

#### 设置触发动作

> 配置 - 动作 - Report problems to Zabbix administrators 启用
