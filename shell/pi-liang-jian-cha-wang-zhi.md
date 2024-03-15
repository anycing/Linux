# 批量检查网址

### 需求：

> 1\. 使用Shell数组的方法实现，检测策略尽量模拟用户访问。
>
> 2\. 每5秒进行一次全部检测，无法访问的输出报警。
>
> 3\. 待检测的地址如下：
>
> http://www.baidu.com
>
> http://www.jd.com
>
> http://www.360.cn
>
> http://127.0.0.1

### 解决方案：

```
#!/bin/bash

. /etc/init.d/functions

urls=(
    'http://www.baidu.com'
    'http://www.jd.com'
    'http://www.360.cn'
    'http://127.0.0.1'
)

wait() {
    for i in `seq 5`
    do
        echo -n '.'
        sleep 1
    done
    echo
}

checkURL() {
    for url in ${urls[@]}
    do
        wget -o /dev/null -T 3 --tries=1 --spider $url > /dev/null 2>&1
        if [ $? -eq 0 ]; then
            action "$url" /bin/true
        else
            action "$url" /bin/false
        fi
        #usleep 500000
    done
}

main() {
    checkCount=1
    while true
    do
        echo "-------- checking $checkCount --------"
        checkURL
        echo -n "Waiting"
        wait
        let checkCount++
    done
}

main
```
