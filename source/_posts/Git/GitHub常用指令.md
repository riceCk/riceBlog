---
title: Git常用指令
tags: Git常用指令
date: 2018-07-27 09:47:00
---
## 欢迎来到GitHub世界，精彩继续
#### 秘钥生成
```bash
ssh-keygen
```

#### 在本地项目文件下创建本地仓库，初始化
```bash
git init
```
#### 绑定用户名邮箱
```bash
git config --global user.name 
```
```bash
git config --global user.email
```
本地用户名邮箱只要第一次上传github的时候设置就可以啦，之后的项目就不用在进行设置，不放心的同学可以下面的代码查询是否已经绑定
#### 查看本地信息
```bash
git config --list
```
#### 在远端git官网上创建一个项目，获取项目的地址，与本地关联
```bash
git remote add origin https://github.com/riceCk/snake.git
```
`origin`是给远端服务器设定的一个名字，并非固定不变的
* git remote -v    查看远端仓库
* git remote rename `<原远端名>` `<现远端名>`   远端仓库重命名
* git remote add `<远端名>` `<远端地址>`  添加源仓库地址为远端仓库

#### git新建分支
1.切换基础分支
```bash
git checkout master
```
2.创建并切换新的分支
```bash
git checkout -b panda
```
3.更新分支代码并提交
```bash
git add *
git commit -m 'init panda'
git push origin panda
```

#### 基础工作流
```bash
git add .   #添加当前目录下的所有文件
```
注意该命令后面有一个“.”,小点。表示把该目录下的所有文件加入到本地暂存区中。执行成功后不会有任何提示：
```bash
git status 
```
该命令会把你本地工作区和暂存区的版本进行比较，查看当前的状态。我下面的状态是已经把所有文件加入到了暂存区中，但是还没有提交到本地历史区。
```bash
git commit -m 'first'
```
该命令会把本地暂存区中的文件提交到本地历史区，注意只有在本地历史区中的内容才能提交到github。执行该命令后，我们所有的文件都只是在本地。没有github任何关系。
```bash
git pull origin master
```
该命令是先把github上的文件拉下来，注意在每次提交之前要首先进行pull，这是防止冲突。
```bash
git push origin master  #master是主干分支的意思
```

* git clone `<远端地址>`克隆仓库
* git clone -b `<分支名称>` `<远端地址>`克隆仓库
* git status查看当前文件的状态
* git add `<工作区文件名>` 将工作区提交暂存区
* git commit -m `<标记>` 提交到本地仓库，name是标记的意思
* git push `<远端名>` `master` 将本地仓库提交到远端仓库，name是定义的远端仓库名，branch是分支名
* git pull `<远程主机名>` `<远程分支名>` : `<本地分支名>` 取回远端的数据与本地分支合并

#### 花式撤销
* git checkout -- `<文件名>` 撤销工作区修改

```bash
git checkout -- index.html
```
将工作区的和暂存区的文件相对比，撤销工作区的内容，最终结果和暂存区一致
```bash
git reset --hart HEAD~1
```

* --hart 回退整个版本
* --mixex 只回退缓存区/本地仓库版本，工作区不变
* --soft 回退当commit之前。工作区和缓存区不变

## 上传中常见的错误
```bash
git pull --rebase origin master

git rebase --skip 
```
[vue上传浏览项目](https://www.jianshu.com/p/13df61095961)

### git无法pull仓库refusing to merge unrelated histories
```bash
git pull origin master ----allow-unrelated-histories
```

