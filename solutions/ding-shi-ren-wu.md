# 定时任务

### 周期性定时任务：cron

#### 开启定时任务守护进程

```
sudo cron -f &
```

```
crontab -e
```

```
# min hour date month week​* * * * **/1 1,10 1-7 * 1-3 sudo cp /var/log/some.log /tmp/$(date +\%Y-\%m-\%d_\%H:\%M:\%S)
```

```
crontab -r
```

#### 系统级别的定时任务添加

```
vim /etc/crontab
```

#### 非交互式追加

```
echo "xxxxxxxxxxxxxx" >> /var/spool/cron/root

chmod 600 /var/spool/cron/root

# 需正确配置权限，否则无效
```

#### 查看日志

```
cat /var/log/cron
```



### 一次性定时任务：at

#### 启动服务

```
systemctl start atd
```

#### 添加任务

```
at 18:00

> echo "hello, world" > /tmp/hello.txt
> ctrl + d
```

#### 查询任务

```
atq
```

