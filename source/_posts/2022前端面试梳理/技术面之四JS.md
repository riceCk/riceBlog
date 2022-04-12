---
title: 技术面之四JS
categories: 2022前端面试梳理
tags: 面试题
date: 2022-04-01 09:48:00
---

# typeof、instanceof、toString的区别
三种都是JS中用于获取数据类型的方法
## typeof
用于笔记基础的数据类型和引用，返回的有**number**、**string**、**boolean**、**null**、**undefined**、**object**
## instanceof
用来判断一个对象是否为某个构造函数的实例
## toString 
想要区别对象或者数组、函数是不行的，**null**和**Array**的结构也是object

```bash
Object.prototype.toString.call(2); // "[object Number]"
Object.prototype.toString.call(''); // "[object String]"
Object.prototype.toString.call(true); // "[object Boolean]"
Object.prototype.toString.call(undefined); // "[object Undefined]"
Object.prototype.toString.call(null); // "[object Null]"
Object.prototype.toString.call(Math); // "[object Math]"
Object.prototype.toString.call({}); // "[object Object]"
Object.prototype.toString.call([]); // "[object Array]"
```

# 什么是作用域链
作用域链分为两种
* 全局作用域：绑定window上，为全部作用域链的最顶层
* 函数作用域：函数执行的时候有一个技术栈，并创建执行环境即执行上下文对象，上下文对象中有一个大对象
* 块级作用域

# 闭包作用
闭包就是通过函数嵌套的方式保证函数内外作用域隔离，以函数内部变量等信息只能在内部读取，并且外部的变量不影响内部
* 可以创建私有变量，通过在外部调用闭包函数访问函数内部的变量
* 使已经运行结束的函数上下文中的变量对象继续留在内存中

## 特性
* 函数嵌套函数
* 函数内部可以引用函数外包的参数和变量（用于链式作用域）
* 参数和变量不会被垃圾回收机制回收

# let、var和const
* var：会将变量的生命提前到代码块顶部，赋值还是在原型的位置，若在赋值前使用，就会出现展示性死去undefined
* let：声明只在当前代码块内，var的声明会被提升到全局
* const：不可重复声明

# JS事件循环
## 解答1
js事件循环的主要部分有一个栈、两个队列、一个幕后线程。
开始的时候，执行栈中会执行全部的同步代码，同步代码中如果有异步函数调用如setTimeout、http请求等，会将对应的请求放到幕后线程去处理，当幕后线程处理完异步请求，比如setTimeout的时间到了，或者http请求获得响应，并且当执行栈中全部执行完毕（为空），会触发一次**事件循环**，将执行宏任务队列中第一个任务，任务执行完毕，执行微任务队列中全部任务。微任务队列全部执行完成后，事件循环再次启动，执行下一个宏观任务，如当前任务队列为空，他会一直等待任务的到来，这叫任务循环
* 宏任务：setTimeout setInterval用户交互操作UI渲染
* 微任务：Proise，nextTick

## 解答2
1.首先js是单线程运行的，在代码执行的时候，通过将不同函数的执行上下文压入执行栈中来保证代码的有序执行
2.在执行同步代码的时候，如果遇到了异步事件，js引擎应不会一直等待其返回结果，而是会将事件挂起，继续执行执行栈中其他任务
3.当同步事件执行完毕后，再将异步事件对应的回调加入到与当前执行栈中不同的另一个任务队列中等待执行。
4.任务队列可以分为宏任务队列和微任务队列，当当前执行栈中事件执行完毕后，js引擎首先会判断微任务队列是否有任务可以执行，如果有就将微任务对首的事件亚茹栈中执行
5.当微任务队列中的任务都执行完成后再将判断宏任务队列中的任务。

# 如何判断两个对象相等
1.递归遍历
2.JSON.stringify

# 前端性能优化
* 算法优化
* CDN
* 图片压缩
* 使用浏览器缓存
* 图片懒加载
* 页面滚动加载
* JS、CSS、HTML等文件压缩
* 开启web服务器的gzip
* 使用nginx方向代理
* 减少DOM操作

JS防抖和节流debounce Throttle
Debounce防抖：在事件最后一次触发n毫秒之内执行事件，不管事件曾被触发过多少次。
Throttle节流：事件在第一次触发后n毫秒内，不论事件是否继续触发，都执行

# for...of 和 for...in的区别
* **for...in**语句以任意顺序迭代对象的可枚举属性（遍历对象key值）
  **for...of**语句遍历可迭代对象定义迭代属性（遍历数组的值，不能遍历对象）

# 线程和进程的区别
* 进程：一个动态过程，是一个活动的实体。简单来说，一个应用程序的运行可以看做是一个进程
* 线程：程序执行流的最小执行单位，是进程中的实际运作单位

# 前端路由实现方式
## Hash路由
Hash路由通过在地址增加`#path`实现区分页面，当hash发生变化后改变对应组件
* window.addEventListener('hashchange', this.refresh, false)可以监听路由变化

## HTML5 HistoryApi
* window.history.back();  后退
* window.history.forward() 前进
* window.history.go(-4)  退后三个页面

# 深拷贝和浅拷贝
## 浅拷贝
* Object.assign()
* 扩展运算符 ...

## 深拷贝
* JSON.stringify()
* 手写循环递归