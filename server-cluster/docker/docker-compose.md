# docker compose

### 安装并配置 docker-compose

```
yum install docker-compose -y

# 需依赖epel源
```

#### 创建所需目录

```
mkdir -p /server/docker/docker-compose

mkdir -p /server/docker/docker-compose/wordpress

cd /server/docker/docker-compose/wordpress
```

#### 编辑docker-compose文件

```
vim docker-compose.yml

# docker-compose 内容如下


version: "3"

services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - web_data:/var/www/html
    ports:
      - "80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
volumes:
    db_data:
    web_data:
```

###

### 安装所需镜像并启动容器

```
docker pull mysql:5.7

docker pull wordpress
```

#### 使用docker-compose启动容器

```
docker-compose up -d

# 需要加-d参数
# 不加-d则会在前台启动，一旦ctrl+c就会关闭容器
# 需要在yml文件所在目录执行命令
```



### 访问测试

```
# 使用浏览器访问 192.168.1.61
```
