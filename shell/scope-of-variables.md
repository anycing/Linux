# Scope of Variables

### shell 运行方式

#### 当前进程运行：

```
source SHELL.sh
```

```
. SHELL.sh
```

#### 子进程运行：

```
bash SHELL.sh
```

```
./SHELL.sh
```

#### demo

```
string=demo_var
```

```
vim demo.sh

#!/bin/bash
echo $string
```

```
chmod u+x demo.sh

source demo.sh
# output: demo_var

. demo.sh
# output: demo_var

bash demo.sh
# output: 

./demo.sh
# output: 
```



### shell 变量作用范围

{% hint style="info" %}
使用 **export** 可以让子shell进程使用当前shell进程的变量
{% endhint %}

shell变量默认作用范围为shell自身进程，子shell进程无法使用

```
export string

# 若要子shell进程可以使用变量，则需使用export进行导出


unset string

# 若不需要时，可使用unset进行删除
```

```
# 可使用上述方式对前面的demo进行测试
```

