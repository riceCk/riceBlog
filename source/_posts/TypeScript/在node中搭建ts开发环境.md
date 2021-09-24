---
title: 2.TS-在node中搭建ts开发环境
categories: TypeScript
tags: TypeScript
date: 2020-05-01 14:36:00
---

# 在node中搭建ts开发环境
```bash
npm install -g typescript
```
上面的指令将会全局安装 Typescript，你可以在任何目录下使用 tsc 指令来执行 TypeScript 的相关代码。
检测是否安装成功，可以使用下面的代码：
```bash
tsc -v
```
## win10需要配置
powershell在win10中的报错：
```bash
tsc : 无法加载文件 C:\Users\administrator\AppData\Roaming\npm\tsc.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.microsoft.com/fwlink/?LinkID=135 
170 中的 about_Execution_Policies。
+ tsc --init
+ ~~~
    + CategoryInfo          : SecurityError: (:) []，PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess
```
错误原因：PowerShell的设置问题，脚本的默认执行策略 Restricted，禁止运行任何脚本和配置文件。

解决方案：更改其执行策略，以**管理员身份**重新打开一个powershell窗口，在窗口中输入指令：
```bash
set-ExecutionPolicy RemoteSigned
```

# 默认情况下，TS会做出下面几个假设：
1.假设当前的执行环境是dom
2.如果代码中没有使用模板化（import、export），便认为改代码是全局执行的
3.编译的目标代码ES3

有两种方式更改以上假设
1.使用tsc命令行的时候，加上选项参数
2.使用ts配置文件，更改编译选项 

# TS的配置文件
使用了配置文件后，使用tsc进行编译时，不能更上文件名，如果跟上文件名，会忽略配置文件。
```bash
tsc --init
```
@type/node
@type是一个ts官方的类型库，其中包含了很多对js代码的类型描述

>JQuery: 用js写的，没有类型检查
>安装@types/jquery,为了jquery库添加类型定义

# 使用第三方库简化流程
ts-node: 将ts代码在内存中完成编译，同时完成运行
```bash
npm i -g ts-node
```
nodemon:编译文件（热更新）
--watch src 检测src文件夹的变化
-e ts 检测什么类型的文件
```bash
nodemon --watch src -e ts --exec ts-node src/index.ts
```

