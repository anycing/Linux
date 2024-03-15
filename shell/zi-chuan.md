# 子串

### 子串表达式及说明

| 表达式                          | 说明                                           |
| ---------------------------- | -------------------------------------------- |
| ${parameter}                 | 返回变量$parameter的内容                            |
| ${#parameter}                | 返回变量$parameter内容的长度（按字符），也适合特殊变量\*           |
| ${parameter:offset}          | 在变量${parameter}中，从位置offset之后开始提取子串到结尾        |
| ${parameter:offset:length}   | 在变量${parameter}中，从位置offset之后开始提取长度为length的子串 |
| ${parameter#word}            | 从变量${parameter}【开头】开始删除最【短】匹配的word子串         |
| ${parameter##word}           | 从变量${parameter}【开头】开始删除最【长】匹配的word子串         |
| ${parameter%word}            | 从变量${parameter}结尾开始删除最短匹配的word子串             |
| ${parameter%%word}           | 从变量${parameter}结尾开始删除最长匹配的word子串             |
| ${parameter/pattern/string}  | 使用string代替第一个匹配的pattern                      |
| ${parameter//pattern/string} | 使用string代替所有匹配的pattern                       |

###

### 例子

```
Will=abcABC123ABCabc
```

```
echo ${Will#a*C}

123ABCabc
```

```
echo ${Will##a*C}

abc
```

```
echo ${Will%a*c}

abcABC123ABC
```

```
echo ${Will%%a*c}


```

```
echo ${Will/abc/ddd}

dddABC123ABCabc
```

```
echo ${Will//abc/ddd}

dddABC123ABCddd
```



### 扩展

| 表达式                | 说明                                             |
| ------------------ | ---------------------------------------------- |
| ${parameter:-word} | 如果parameter变量为空或未定义，则返回word字符串替代变量的值           |
| ${parameter:=word} | 如果parameter变量为空或未定义，则设置这个变量值为word，并返回其值        |
| ${parameter:?word} | 如果parameter变量为空或未定义，则将word字符串作为标准错误输出，否则输出变量的值 |
| ${parameter:+word} | 如果parameter变量为空或未定义，则什么都不做，否则用word字符串的值替换变量的值  |
