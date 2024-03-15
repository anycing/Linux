# Git服务程序的基本使用

### 安装Git

```
yum install git -y
```



### 配置Git

> 首次安装Git服务程序后需要设置下用户名称、邮件信息和编辑器，这些信息会随着文件每次都提交到Git数据库中，用于记录提交者的信息；
>
> 而Git服务程序的配置文档通常会有三份，针对当前用户和指定仓库的配置文件优先级最高：

#### Git配置文件

| 配置文件                                           | 作用                  |
| ---------------------------------------------- | ------------------- |
| /etc/gitconfig                                 | 保存着系统中每个用户及仓库通用配置信息 |
| <p>~/.gitconfig</p><p>~/.config/git/config</p> | 针对于当前用户的配置信息        |
| 工作目录/.git/config                               | 针对于当前仓库数据的配置信息      |

#### 配置用户名称、电子邮件、默认文本编辑器

```
git config --global user.name "Xuhao Diao"

git config --global user.email "anycing@gmail.com"

git config --global core.editor vim

# 查看配置环境信息
git config --list
```



### 提交数据

#### 初始化指定目录为Git的工作目录

```
mkdir wills

cd wills/

git init
```

#### 将该文件添加到暂存区

```
echo "Hello Git" > readme.txt

git add readme.txt

echo "Something not important" >> readme.txt

git commit -m "add the readme file"
```

#### 查看当前工作目录的状态

```
git status
```

#### 查看当前文件内容与Git版本数据库中的差别

```
git diff readme.txt
```

#### 把文件提交到Git版本数据库

```
git add readme.txt

git commit -m "added a line of words"

git status
```

#### 设置要忽略上传的文件（写入到"工作目录/.gitignore"文件中）

| 配置写法         | 说明                   |
| ------------ | -------------------- |
| \*.a         | 忽略所有以.a为后缀的文件        |
| !lib.a       | 但是lib.a这个文件除外，依然会被提交 |
| build/       | 忽略build目录内的所有文件      |
| build/\*.txt | 忽略build目录内以txt为后缀的文件 |
| git.c        | 指定忽略名字为git.c的文件      |

```
touch git.c

vim .gitignore
git.c

git add .

git commit -m "add the .ignore file"
```

#### 一步到位提交数据

```
echo "Modified again" >> readme.txt

# 使用 -a 参数，Git会将以前所有追踪过的文件添加到暂存区后自动的提交
git commit -a -m "Modified again"
```

#### 强制添加文件

```
git add -f git.c
```

#### 重新提交一次

```
git commit --amend
:wq
```



### 移除数据

#### 添加文件到暂存区

```
touch file1

git add file1

git status
```

#### 将该文件从Git暂存区域的追踪列表中移除（并不会删除当前工作目录内的数据文件）

```
git rm --cached file1

ls

git status
```

#### 将文件数据从Git暂存区和工作目录中一起删除

```
git add .

git rm -f file1

ls

git status
```



### 移动数据

#### 方法一：使用 git mv 命令

```
git mv readme.txt introduction.txt

ls

git status

git commit -m "rename readme.txt"
```

#### 方法二：重命名、再删除git跟踪、再添加、提交

```
mv introduction.txt readme.txt

git rm introduction.txt

git add readme.txt

git commit -m "rename introduction.txt"
```



### 历史记录

#### 查看所有提交的历史记录

```
git log
```

#### 查看最近2条历史记录

```
git log -2
```

#### 查看最近一次提交的内容的差别

```
git log -p -1
```

#### 查看最近两次的增改行概要信息

```
git log --stat -2
```

#### 以每行显示一条提交信息显示历史记录

```
git log --pretty=oneline
```

#### 以更详细的模式输出最近两次的历史记录

```
git log --pretty=fuller -2
```

#### 使用format参数来指定具体的输出格式（便于后期编程的提取分析）

| 写法  | 说明               |
| --- | ---------------- |
| %s  | 提交说明             |
| %cd | 提交日期             |
| %an | 作者的名字            |
| %cn | 提交者的姓名           |
| %ce | 提交者的电子邮件         |
| %H  | 提交对象的完整SHA-1哈希字串 |
| %h  | 提交对象的简短SHA-1哈希字串 |
| %T  | 树对象的完整SHA-1哈希字串  |
| %t  | 树对象的简短SHA-1哈希字串  |
| %P  | 父对象的完整SHA-1哈希字串  |
| %p  | 父对象的简短SHA-1哈希字串  |
| %ad | 作者的修订时间          |

```
git log --pretty=format:"%h %cn"
```



### 还原数据

#### 修改文件并提交Git版本数据库

```
echo "Git is a version control system" >> readme.txt

git commit -a -m "Introduce software"

git log --pretty=oneline
```

#### 还原到上一个版本

```
git reset --hard HEAD^
# 上一个提交版本会叫HEAD^
# 上上一个版本则会叫做HEAD^^
# HEAD~5来表示往上数第五个提交版本

cat readme.txt
```

#### 还原到最新提交的版本

```
# 此命令查不到 git log --pretty=oneline
# 需使用：
git reflog


# 找到历史还原点的SHA-1值
git reset --hard 5d3c875


cat readme.txt
```

#### 还原某个文件内容

```
echo "Some wrong words" >> readme.txt

git checkout -- readme.txt

cat readme.txt

# checkout 规则是如果暂存区中有该文件，则直接从暂存区恢复；
# 如果暂存区没有该文件，则将还原成最近一次文件提交时的快照。
```



### 标签管理

> 当版本仓库内的数据有个大的改善或者功能更新，我们经常会打一个类似于软件版本号的标签，这样通过标签就可以将版本库中的某个历史版本给记录下来，方便我们随时将特定历史时期的数据取出来用，另外打标签其实只是向某个历史版本做了一个指针

#### 给最近一次提交的记录打个标签

```
git tag v1.0
```

#### 查看所有的已有标签

```
git tag
```

#### 查看此标签的详细信息

```
git show v1.0
```

#### 创建带有说明的标签，用-a指定标签名，-m指定说明文字

```
git tag -a v1.1 -m "version 1.1 released"
```

#### 删除标签

```
git tag -d v1.0
# 我们为同一个提交版本设置了两次标签，来把之前的标签v1.0删除
```
