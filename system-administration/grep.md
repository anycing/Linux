# GREP

### grep 三种正则表达式引擎

| Option | Description      |
| ------ | ---------------- |
| -E     | POSIX扩展正则表达式，ERE |
| -G     | POSIX基本正则表达式，BRE |
| -P     | Perl正则表达式，PCRE   |

### grep 常用选项

| Option       | Description                          |
| ------------ | ------------------------------------ |
| -b           | 将二进制文件作为文本来进行匹配                      |
| -c           | 统计以模式匹配的数目                           |
| -i           | 忽略大小写                                |
| -n           | 显示匹配文本所在行的行号                         |
| -v           | 反选，输出不匹配行的内容                         |
| -r           | 递归匹配查找                               |
| -A n         | n为正整数，表示after的意思，除了列出匹配行之外，还列出后面的n行  |
| -B n         | n为正整数，表示before的意思，除了列出匹配行之外，还列出前面的n行 |
| --color=auto | 将输出中的匹配项设置为自动颜色显示                    |

```
echo 'www.baidu.com\nwww.google.com\nwww.will-say.com' | grep -E 'www\.(will-say|google)\.com'

# www.google.com
# www.will-say.com
```

