# Git基础命令

### 建Git仓库

#### 场景一：把已有的项目代码纳入Git管理

```bash
cd <dir>
git init
```

#### 场景二：新建的项目直接用Git管理

```bash
cd <dir>

git init your_project
# 会在当前路径下创建和县哪个木名称同名的文件夹

cd your_project
```



### 案例演示

#### 准备项目文件

```bash
# 下载及解压项目代码文件
curl -o /tmp/0-material.zip https://git201901.github.io/github_pages_learning/docs/0-material.zip
unzip /tmp/0-material.zip -d /tmp

# 准备项目文件夹
mkdir /wills
cd /wills
```

#### 初始化项目

```bash
git init git_learning

cd git_learning
```

#### 添加文件至项目

```bash
# 添加测试文件
echo "hello Git" > readme
git add readme
git commit -m "Add readme file"


# 添加首页和logo
cp /tmp/0-material/index.html.01 index.html
cp -r /tmp/0-material/images/ .
# 提交
git add index.html images/
git commit -m 'Add index + logo'


# 添加样式
mkdir styles
cp /tmp/0-material/styles/style.css.01 styles/style.css
# 提交
git add styles/
git commit -m 'Add style'


# 添加js
cp -r /tmp/0-material/js/ .
# 提交
git add js/
git commit -m 'Add js'


# 查看历史日志
git log
```

#### 修改文件

```bash
echo "copyright 2020 Will" >> index.html

git add -u
# -u 把Git已经跟踪的文件一起提交到暂存区
git commit -m 'Add refering projects'
```

#### 重命名文件

```bash
git mv readme readme.md
git commit -m 'Rename readme to readme.md'
```



### 通过 git log 查看版本演变历史

```bash
git log 
```

#### 查看最近2条历史记录

```bash
git log -n 2
```

#### 查看简洁版信息

```bash
git log --oneline

git log --pretty=oneline

# 区别在于第一个的哈希值短一些（只显示7位），第二种显示完整的哈希值
```

#### 查看所有分支的版本历史

```bash
git log --all
# 这样显示的信息不存在父子关系，直接看看不出
# 一般加 --graph 参数

git log --all --graph
```

