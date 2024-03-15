# 批量修改文件名

### 需求：

> /web 目录下有一批文件名由固定字符串 "`music`" + 10个随机数字字母组成的html文件（这批html文件是由下面的 makeHTML.sh 生成的）
>
> 现在需要将这批html文件的文件名中固定字符串 "`music`" 替换为 "`movie`"
>
> 后缀的小写 "`html`" 替换为 "`HTML`"

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



### 解决方案：

#### 方案一：使用 for 循环

```
webDir=/web

cd $webDir

for oldName in `ls`
do
    newName=`echo $oldName | sed 's/music/movie/g' | sed 's/html/HTML/g'`
    mv $oldName $newName
done
```

#### 方案二：使用 rename 命令

```
rename "music" "movie" /web/*

rename "html" "HTML" /web/*
```

#### 方案三：使用 awk 拼接命令后传给 bash 执行

```
cd /web/

ls | awk -F '[_.]' '{ print "mv", $0, "movie_"$2".HTML" }' | bash
```

