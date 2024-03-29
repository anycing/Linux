# location

### 匹配标识

| 匹配标识  | 说明             |
| ----- | -------------- |
| =     | 精确匹配           |
| \~    | 区分大小写          |
| \~\*  | 不区分大小写         |
| !\~   | 对\~取反          |
| !\~\* | 对\~\*取反        |
| ^\~   | 字符串匹配不做正则表达式检查 |



### URI及特殊字符组合匹配顺序

| 优先级 | 示例                                  | 匹配说明                  |
| --- | ----------------------------------- | --------------------- |
| 1   | location  = / {                     | 精确匹配 /                |
| 2   | location ^\~ /images/ {             | 匹配常规字符串，不做正则匹配检查      |
| 3   | location \~\* \\.(gif\|jpg\|png)$ { | 正则匹配                  |
| 4   | location /documents/ {              | 匹配常规字符串，如果有正则则优先匹配正则  |
| 5   | location / {                        | 所有location都不能匹配后的默认匹配 |



### 测试

```
# 可在server中添加如下代码进行测试

    location / {
        return 400;
    }

    location = / {
        return 401;
    }

    location ^~ /images/ {
        return 402;
    }

    location ~ \.(gif|jpg|png)$ {
        return 403;
    }

    location /documents/ {
        return 404;
    }
```



### 企业应用实例

> **匹配静态扩展名，然后把请求发给静态服务器，实现动静分离**

