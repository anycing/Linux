# Nginx rtmp Project

## Nginx 编译安装直播项目实战

###

### 编译安装服务

#### 安装下载与编译所需工具

```
yum install git gcc make pcre-devel openssl-devel -y
```

#### 下载源码

```
cd /usr/local


git clone git://github.com/arut/nginx-rtmp-module.git

# rtmp 为直播推流模块

wget https://nginx.org/download/nginx-1.16.1.tar.gz
```

#### 配置及编译

```
tar xf nginx-1.16.1.tar.gz

cd nginx-1.16.1/

./configure --with-http_ssl_module --add-module=../nginx-rtmp-module

make && make install
```

#### 验证

```
cd /usr/local/nginx/sbin

./nginx -V
./nginx


curl http://192.168.1.61
```



### 配置 rtmp 并启动服务

```
vim /usr/local/nginx/conf/nginx.conf

# 增加如下参数，一般增加在http上边

rtmp {
    server {
        listen 1935;
        chunk_size 5000;

        application hls {
            live on;
            hls on;
            record off;
            hls_path /usr/local/nginx/html/hls;
            hls_fragment 3s;
        }
    }
}
```

```
# 停止服务

/usr/local/nginx/sbin/nginx -s stop

或

pkill nginx
```

```
# 启动并加载配置文件

/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
```



### 直播推流

#### 客户端下载直播软件

```
https://obsproject.com/
```

#### 推流设置

```
# 自定义推流地址 service 为 自定义
# server 为：

rtmp://192.168.1.61:1935/hls

# 1935 为rtmp端口， hls 为 nginx.conf 中配置的 application 的值

# stream key 为自定义的值
will
```

#### 客户端访问

```
# Mac 系统直接使用 Safari 访问如下地址即可观看直播

http://192.168.1.61/hls/will.m3u8
```

```
# Windows 系统需要如下html代码才可观看

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>PC HLS video</title>
    <link href="http://cdn.bootcss.com/video.js/6.0.0-RC.5/alt/video-js-cdn.min.css" rel="stylesheet">
</head>
<body>

<h1>PC 端播放 HLS(<code>.m3u8</code>) 视频</h1>
<p>借助 video.js 和 videojs-contrib-hls</p>
<p>由于 videojs-contrib-hls 需要通过 XHR 来获取解析 m3u8 文件, 因此会遭遇跨域问题, 请设置浏览器运行跨域</p>

<video id="hls-video" width="300" height="200" class="video-js vjs-default-skin"
       playsinline webkit-playsinline
       autoplay controls preload="auto"
       x-webkit-airplay="true" x5-video-player-fullscreen="true" x5-video-player-typ="h5">
    <!-- 直播的视频源 -->
    <source src="https://9e284abd9f17ac5160126ad0502be6f6.v.smtcdns.net/tlivecloud-cdn.ysp.cctv.cn/228B6063D0A1BC4CABA45245F2BB7E7FDB1B387F648A2B5C75065CFAF447517486A6D5E7C2A9F0838BA80ABE4B7AD2A0DFD5DE2E6BF241CE57CC5AE46444D81D7704852BDE3967BC8863AA0DC02887C11426D3562B5A0E9835676E5D5B3421F0/2000210101.m3u8" type="application/x-mpegURL">
    <!-- 点播的视频源 -->
    <!--<source src="http://devstreaming.apple.com/videos/wwdc/2015/413eflf3lrh1tyo/413/hls_vod_mvp.m3u8" type="application/x-mpegURL">-->
</video>

<script src="http://cdn.bootcss.com/video.js/6.0.0-RC.5/video.js"></script>
<!-- PC 端浏览器不支持播放 hls 文件(m3u8), 需要 videojs-contrib-hls 来给我们解码 -->
<script src="http://cdn.bootcss.com/videojs-contrib-hls/5.3.3/videojs-contrib-hls.js"></script>
<script>
    // XMLHttpRequest cannot load http://xxx/video.m3u8. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://192.168.198.98:8000' is therefore not allowed access.
    // 由于 videojs-contrib-hls 需要通过 XHR 来获取解析 m3u8 文件, 因此会遭遇跨域问题, 请设置浏览器运行跨域
    var player = videojs('hls-video');
    player.play();
</script>
</body>
</html>
```

