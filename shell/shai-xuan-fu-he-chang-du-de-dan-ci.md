# 筛选符合长度的单词

### 需求：

> 利用 bash for 循环，打印下面这句话中字母数不大于6的单词
>
> The San Francisco–Oakland Bay Bridge half dollar is a fifty-cent piece struck by the United States Bureau of the Mint in 1936 as a commemorative coin.



### 解决方案

#### filterWord.sh

```
sentence="The San Francisco–Oakland Bay Bridge half dollar is a fifty-cent piece struck by the United States Bureau of the Mint in 1936 as a commemorative coin."

for word in $sentence
do
    if [ ${#word} -lt 6 ]; then
        echo $word
    fi
done
```

```
sentence="The San Francisco–Oakland Bay Bridge half dollar is a fifty-cent piece struck by the United States Bureau of the Mint in 1936 as a commemorative coin."

for word in $sentence
do
    length_word=`echo $word | wc -L`
    if [ $length_word -lt 6 ]; then
        echo $word
    fi
done
```



### 获取字符串字符数的方法：

```
word=hello

echo ${#word}
```

```
echo $word | wc -L
```

```
expr length $word
```

```
echo $word | awk '{ print length }'
```

