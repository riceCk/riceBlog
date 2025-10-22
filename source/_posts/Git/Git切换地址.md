---
title: Git切换地址
tags: Git常用指令
date: 2018-10-29 19:22:00
---

# Git 切换地址

## 🔄 第一步：切换 Git 远程地址

```bash
# 查看当前远程地址
git remote -v

# 切换到新的远程仓库地址
git remote set-url origin ssh://xxxx.git

# 验证切换成功
git remote -v
```

## 📥 第二步：获取新仓库的分支信息

```bash
# 从新远程仓库获取所有分支和标签;可能需要进行账号和密码验证
git fetch origin
```

## 🌿 第三步：尝试切换到目标分支（遇到问题）

```bash
# 尝试切换到新仓库的masterRz分支
git checkout -b masterRz origin/masterRz
```

**问题：发现新分支是五个月前的代码，没有修改**

## 🔙 第四步：回到原分支恢复代码

```bash
# 切换回main分支（包含您的所有修改）
git checkout main
```

## ⚠️ 第五步：尝试合并（遇到大量冲突）

```bash
# 切换到masterRz分支
git checkout masterRz

# 尝试合并main分支
git merge main --allow-unrelated-histories
```

**问题：出现大量合并冲突，因为两个仓库历史记录不相关**

## 🚫 第六步：取消合并，使用替代方案

```bash
# 取消合并操作
git merge --abort

# 尝试复制特定文件
git checkout main -- src/pages/inspection/Inspections/abnormalDeviceDaily.tsx src/pages/equipment/fix/deviceFaultTop10.tsx src/pages/equipment/fix/monthlyDeviceRepair.tsx src/pages/equipment/fix/monthlyDeviceMaintenance.tsx src/components/ECharts/

# 提交更改
git commit -m "feat: 添加设备统计相关页面和ECharts组件"

# 推送到远程
git push origin masterRz
```

## ✅ 第七步：最终解决方案（强制替换）

```bash
# 回到main分支
git checkout main

# 删除有问题的masterRz分支
git branch -D masterRz

# 基于main分支重新创建masterRz分支
git checkout -b masterRz

# 强制推送，完全替换远程分支
git push origin masterRz --force

# 设置分支跟踪
git branch --set-upstream-to=origin/masterRz masterRz
```
