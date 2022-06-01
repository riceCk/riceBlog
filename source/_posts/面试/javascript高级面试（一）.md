---
title: javascript高级面试
categories: 面试题
tags: 面试题  
date: 2018-12-23 17:14:00
---
# ES6
## ES6 模块化如何使用，开发环境如何打包
* 模块化的基本语法
* 开发环境配置
* 关于js众多模块化标准

## 模块化基本语法
* 没有模块化
* AMD成为标准，require.js (也是CMD)
* 前端打包工具，node.js模块化可以被使用
* ES6出现，想统一现在所有模块化标准
* nodejs积极支持，浏览器尚未统一
* 你可以制造lib，但是不要制造标准



## 开发环境配置
* 语法：import export (注意有无default)
* 环境：babel编译ES6语法，模块化可用webpack和rollup

### export 语法
```bash
# util1.js
export default {
  a: 100
}

# util2.js
export function fn1 () {
  alert('fn1')
}
export function fn2 () {
  alert('fn2')
}

# index.js
import util1 from './util1.js'
improt {fn1, fn2} from './util2.js'
console.log(util1)
fn1()
fn2()
```

## Class 和 普通构造函数的区别
* Class 在语法上更加贴合面向对象的写法
* Class 实现继承更加易读、易理解
* 更易于写java等后端语言的使用
* 本质还是语法糖，使用的prototype

### JS 构造函数
```bash
function MathHandle(x, y) {
  this.x = x
  this.y = y
}
MathHandle.prototype.add = function () {
  return this.x + this.y
}
var m = new MathHandle(1, 2)
console.log(m.add())
```

### Class基本语法
```bash
class MathHandle {
  constructor(x, y) {
    this.x = x;
    this.y = y
  }
  add () {
    return this.x + this.y
  }
}
const m = new MathHandle(1, 2)
console.log(m.add())
```

# 异步
* 《前端js面试技巧》讲到异步基础
* 高级面试会问道更多的内容
* 如event-loop Promise Async/await等

## 问题
* 什么是单线程，异步有什么关系
* 什么是event-loop
* 是否用过jquery的deferred
* Promise 基本使用和原理
* 介绍一下async/await（和Promise的区别和关系）

### 什么是单线程，异步有什么关系
* 单线程-只有一个线程，只能做一件事情
* 原因-避免DOM渲染冲突
* 解决方案-异步

#### 解决方案-异步
* 问题一：没有按照书写方式执行，可读性差
* 问题二：callback中不容易模块化

#### 问题解答
* 单线程就是同时只做一件事，两段js不能同时执行
* 原因就是为了避免DOM渲染的冲突
* 异步是一种“无奈”的解决方案，虽然有很多问题

### 什么是event-loop
* 事件轮询，js实现异步的具体解决方案
* 同步代码，直接执行
* 异步函数先放在异步队列中
* 待同步函数执行完毕，轮询执行异步队列的函数

# MVVM和Vue

## 使用jQuery和使用框架的区别
* 数据和视图的分离，解耦
* 以数据驱动视图，只关心数据变化，DOM操作被封装

## 什么是MVVM
* MVVM：Model、View、ViewModel
* 三者之间的联系，以及如果对应到各段代码
* ViewModel的理解，联系View和Model

### MVVM三要素
* 响应式：vue如何监听到data的每个属性变化？
* 模板引擎：vue的模板如何被解析，指令如何处理？
* 渲染：vue的模板如何被渲染成html？以及渲染过程

### vue中如何实现响应式
* 关键是理解Object.definProperty
* 将data的属性代理到vm上

### vue如何解析模板
* 模板：字符串、有逻辑、嵌入js变量
* 模板必须转换成js代码（有逻辑，渲染html，js变量）
* render函数什么样子的
* render函数执行是返回vnode
* updateComponent


### 什么是响应式
* 修改data属性之后，vue立刻监听到
* data属性被代理到vm上

## vue如何解析模板
* 模板是什么
* render函数
* render函数与vdom

### 模板是什么
* 本质：字符串
* 有逻辑，如v-if、v-for等
* 与html格式很像，但是有很大区别
* 最终还要转换为html来显示
* 模板最终必须转换成js代码
* 有逻辑（v-if,v-for）必须用js才能实现（图灵完备）
* 转换为html渲染页面，必须用js才能实现
* 因此，模板最重要转换成一个js函数（render函数）

### render函数
* 从哪里可以看到render函数？
* 复杂一点的例子，render函数是什么样子的？
* v-if,v-for,v-on都是怎么处理的？



# 组件化与react
* 说一下对组件化的理解
* jsx本质是什么
* jsx和vdom的关系
* 说一下setState的过程
* 阐述自己对react和vue的认识

## jsx本质是什么
* jsx语法（标签、js表达式、判断、循环、事件绑定）
* jsx是语法糖，需被解析成js才能运行
* jsx是独立的标准，可被其他项目使用

## jax和vdom的关系
* 分析：为何需要vdom
* React.createElement 和 h
* 何时 patch ？

### 为何需要vdom
* vdom是React初次推广开来的，结合jsx
* jsx就是模板，最终渲染成html
* 初次渲染 + 修改state后的re-render
* 正好符合vdom的应用场景
 
# hybrid

## hybrid是什么，为何会用hybrid
* hybrid文字解释
* 存在价值，为何会用hybrid
* webview
* file://协议
* hybrid实现流程

### bydrid文字解释
* hybrid即使“混合”，即前端和客户端混合开发
* 需前端开发人员和客户端开发人员配合完成
* 某些环境也可能涉及到server端

### hydrid存在价值
* 可以快速迭代更新【关键】（无需app审核）
* 体验更流畅（和NA的体验基本类似）
* 减少开发和沟通成本，双端公用一套代码

### file 协议
* 其实在一开始接触html开发，就已经使用了file协议
* 只不过你当时没有“协议”“标准”等这些概念
* 再次强调“协议”“标准”的重要性

### hybrid实现流程
* 不是所有场景都适合使用hybrid
* 使用NA：体验要求极致，变化不频繁（如头条的首页）
* 使用hybrid：体验要求高，变换频繁（如头条新闻详情页）
* 使用h5：体验无要求，不常用（如举报，反馈等页面）

## hydrid 更新上线流程
* 分版本，有版本号，如：202001081113
* 将静态文件压缩成zip包，上传到服务器
* 库弧度每次启动，都去服务器检查版本号
* 如果服务器版本号大于客户端版本号，就去下载最新的zip包
* 下载完之后解压包，然后将现有文件覆盖

## hybrid和h5的区别
### 优点
* 体验更好，跟NA体验基本一致
* 可快速迭代，无需app审核【关键】

### 缺点
* 开发成本高。联调、测试、查bug都比较麻烦
* 运维成本高。

### 适用场景
* hybrid：产品的稳定功能，体验要求比较高，迭代频繁
* h5：单次的运营活动（如：xx红包，举报页面）或不常用功能