# Git管理分支结构

### 分支管理

#### 创建名为branch01的分支

```bash
git branch branch01

# 一条命令创建并切换分支
# git checkout -b branch01
```

#### 切换至分支branch01

```
git checkout branch01
```

#### 查看当前分支

```
git branch
```

#### 切回主分支

```
git checkout master
```

#### 合并分支

```
git merge branch01
```

#### 删除branch01分支

```
git branch -d branch01
```



### 内容冲突解决

```bash
# 切换至主分支
git checkout master

# 删除< <<<<<<，=======，>>>>>>>所在的行

# 再次添加冲突的文件
git add readme.txt

# 提交
git commit -m "conflict fixed"
```

#### 查看Git历史提交记(可以看到分支的变化)

```bash
git log --graph --pretty=oneline --abbrev-commit
```
