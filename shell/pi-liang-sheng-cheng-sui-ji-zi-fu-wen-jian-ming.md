# 批量生成随机字符文件名

### 需求：

> 使用 for 循环在 /web 目录下 创建10个 html 文件，每个文件名以固定字符串 "`music`" 作为前缀，然后接10位随机字符串（包含数字和大小写字母），以 .html 作为文件后缀
>
> 文件名示例：music\_xyYO6c7uwY.html



### 解决方案：

#### makeHTML.sh

```
webDir=/web

if [ -d $webDir ]; then
    rm -rf $webDir/*
else
    mkdir $webDir
fi

for n in `seq 10`
do
    random=`openssl rand -base64 32 | sed 's/[^0-9a-zA-Z]//g' | cut -c 1-10`
    touch $webDir/music_${random}.html
done
```
