# SED

### sed 常用选项

| Option      | Description                             |
| ----------- | --------------------------------------- |
| -n          | 安静模式，只打印受影响的行，默认打印输入数据的全部内容             |
| -e          | 用于在脚本中添加多个执行命令一次执行，在命令行中执行多个命令通常不需要加该参数 |
| -f filename | 指定执行filename文件中的命令                      |
| -r          | 使用扩展正则表达式，默认为标准正则表达式                    |
| -i          | 将直接修改输入文件内容，而不是打印到标准输出设备                |

### sed 执行命令

| Command | Description       |
| ------- | ----------------- |
| s       | 行内替换              |
| c       | 整行替换              |
| a       | 插入到指定行的后面         |
| i       | 插入到指定行的前面         |
| p       | 打印指定行，通常与-n参数配合使用 |
| d       | 删除指定行             |



> sed -\<opt> '\<n>\<cmd>\<str>\<range>\<opr>' \<FILENAME>



### **sed Examples**

#### **增**

```
sed -i '2a Hello world!' test.txt

# 在test.txt文件内容第2行后边追加字符串"Hello world!"（追加在了第3行）


sed -i '2i Hello world!' test.txt

# 在test.txt文件内容第2行追加字符串"Hello world!"（追加在了第2行，原先的内容下移）
```

#### **删**

```
sed -i '2d' test.txt

# 删除第2行


sed -i '5,8d' test.txt

# 删除5-8行


sed '/keyword/d' test.txt

# 删除所有test.txt文件内容中所有包含字符串keyword的行，
# 只打印不保持文件，保持文件需要加-i选项。
```

#### **改**

```
sed -i 's/sad/happy/g' test

# 替换test.txt文件内容中的所有字符串sad为字符串happy并保持文件
# g 表示全局范围


sed -e 's#keyword1#keyword2#g' -e 's#keyword3#keyword4#g' test.txt
# -e 多个执行命令一次执行
```

#### **查**

```
sed -n '2,5p' test.txt

# 打印test.txt文件内容的2-5行


sed -n "/keyword/p' test.txt

# 过滤出test.txt文件内容中含有字符串keyword的行
```

