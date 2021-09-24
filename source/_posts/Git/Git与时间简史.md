---
title: Git与时间简史
tags: Git常用指令
date: 2018-10-28 19:22:00
---

# 怎么学好Git
* 保持好奇
* 学会提问
* 了解背景

```bash
主机 路径 文件名 （三维空间） -- 文件在哪
时间概念： （四维时空） -- 文件变化过程（回到过去）
平行时空（宇宙）： （五维时空）虫洞 -- 自我的并行开发
平行宇宙： （六维时空）： -- 人与人之间的并行开发
```
# git常规命令
```bash
git clone xxx.git  # 克隆（开天辟地）创建本地仓库
git add .          # 添加代码
git pull           # 拉去更新代码
git commit -am "xxxx"  # 推送本地仓库（我们的宇宙）
git push           # 推送到远程仓库
git merge          # 分支合并（时空交融）
git checkout       # 切换分支
git reset          # 回退版本（回到过去）
git branch         # 查看分支
git checkout -b test1 # 创建test1分支
git checkout master # 转换时空
```

## 具体流程
```bash
git clone xxx.git
vim a.txt    # 编辑内容
git add .    # 添加到工作区
git checkout -b test1/test2  # 创建分支
git checkout test1/test2/master # 切换各个分支，在各个分支更新代码
git commit -am 'xxxx'   # 在各个分支上提交各个时空的代码，可以添加备注
git checkout master # 为了整合分支，切换到master主分支上
git merge test1   # 在master主分支上合并test1上的代码
git merge test2   # 继续合并test2很有可能产生冲突
vim a.txt        # 进入文件，对有冲突的部分进行手动删除修改
git commit -am '解决冲突再次提交'  # 对冲突的文件修改后要再次提交一遍
git pull         # 拉去最新代码
git push         # 上传代码
git log          # 可以查看提交的各个版本
git reset --hard cf85f075a70e9fef916c5c2b18e4d2a245223f66 # 指定回到那个版本
```

# 算法导论
## 排序（冒泡，选择，归并，快速排序，堆排序）
时间复杂度，文件复杂度
## 树（数据结构）
二叉树，二叉平衡树，红黑树，B-树，B+树
属性结构的遍历（深度优先搜索，广度优先搜索）
前序遍历，中序遍历，后序遍历（随意给两个还原一棵树）
## 图论
最小生成树问题（普里姆算法，克鲁斯卡算法）
最短路径问题（迪杰斯特拉算法，弗洛伊德算法）

## 动态规划问题