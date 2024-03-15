# 数据持久化

### 在宿主机与容器之间拷贝文件

```
docker container cp /tmp/hello confident_newton:/usr/share/nginx/html/

# 拷贝宿主机上的 /tmp/hello 至容器 confident_newton 的/usr/share/nginx/html/下
```



### 启动容器时实现挂载

```
docker run -d -p 80:80 -v /tmp/hello:/usr/share/nginx/html nginx
```



### 数据卷管理

```
docker run -d -p 80:80 -v hello:/usr/share/nginx/html nginx

# -v 卷名:/data（第一次卷是空的，会自动创建卷，同时会把容器中的数据复制到卷中）
# 如果卷中有数据，会把卷中的数据挂载到容器中
```
