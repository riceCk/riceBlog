---
title: node.js
categories: 了解nodejs前世今生
tags: node
date: 2018-07-23 09:47:00
---

# 在学习nodejs之前不妨先探讨一下问什么要学？怎么学？
## 1、主动提问
## 2、科学思考
### Eg：我们为什么要学NOdeJs?
* NodeJs很火？公司招聘很多要求NodeJs？
* NodeJs为什么火？

## 3、NodeJs和Javascript有什么区别？
## NodeJs是一个平台，JavaScript是一个语言

### (1) 用户端语言：

* 浏览器： Html，Css，Js
* 安卓：Java，Python，C++，C#
* IOS：Objective-c，C++
* WindowsPC: C,C++,VB

### (2) 服务端软件
Java，C++， NodeJs， Python, Php, C
### (3) 什么时候计算机语言？什么是编程语言
* 编程语言`console.log("hello world")` ，计算机读不懂，普通人类看不懂，只有程序员懂的语言
* 编译过程：词法分析、语法分析、语义分析

### (4) 什么是词法分析
识别关键字、标识符、分界符、运算符
* 关键字：var,if,else,function,return,break
* 标识符：var a = 1; a就是标识符；function fn(); fn就是标识符
* 分界符： ；{} ()
* 运算符： +-*/=

### (5)如果一个语言X和一个语言Y拥有相同的词法分析，那么X和Y是同一种语言吗？
不是
### (6) 什么时候语法分析
将代码转化为命令语句或者短语
### (7)如果一个语言X和语言Y拥有相同的语法分析，那么X和Y是同一种语言吗？
NOdeJs和JavaScript
### (8) 什么是语义分析
程序作用在操作系统上，生成计算机操作系统能够执行的程序
## 4、NodeJs在什么情况下出现的背景
浏览器大战
### (1)世界上第一个普通使用并且可以展示图片的浏览器是什么？
* NCSA Mosaic  蒂姆伯纳斯·李
* NCSA_Mosaic/2.0(windows 3.1)
* Netscape Communication Corporation
* Mozilla Mosaic + killer   Mosaic Godzilla
* 渲染引擎 Gecko
* Chrome V8 渲染引擎

### (2)NodeJs为什么选择Js和V8引擎？
* Ryan Dah1

### (3) NodeJs是不是单线程
绝对是多线程的，但是将多线程分装的底层，所以感觉到是单线程

## 5、全栈工程师都需要会什么？
浏览器->反向代理->服务->Cache->数据库
* (1)前端语言(html、css、js)
* (2)后端语言(NodeJs)
* (3)网络知识
* (4)持久化
* (5)全栈无止境
![全栈图](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/1.png)

## 6、前后端业务
### （前端 + Web层高并发） + （业务层复杂的业务逻辑 + 持久层大吞吐量）
在之前的前端还未发展后三个层都是由后端开发的，用的后端语言，但是导致后端压力过大，随着前端的发展，**Web层高并发**逐渐由前端承担，组成交互层，但是**Web层高并发**是由后端语言所写的数据逻辑，前端在学一门后端语言成本太高，这时候**node.js**的诞生，虽然**node.js**并不是最好的语言，但是面对现今的时候确实最合适的，这就是**node.js这么火**的原因，但是随着时代的发展，**node**极有可能面临淘汰，所以学习**node**重要的学习后端思想