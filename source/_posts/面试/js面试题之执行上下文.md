---
title: js面试题之执行上下文
categories: 面试题
tags: 面试题
date: 2018-10-10 17:32:00
---

# 执行上下文
> JS原理：原型链、执行上下文、事件循环

## 预备知识
### 1、栈 --- 后进先出
栈类似数组的一种数据结构

### 2、对象引用
如果变量是一个对象，实际上变量中存放的是对象的地址

## 执行上下文
执行上下文：某个函数或全局代码的执行环境，该环境中包含执行代码需要的所以信息。简单理解，**执行上下文就是一个对象，对象中包含了执行代码需要的信息**
当执行一个函数时，需要建立执行上下文，建立之后，才正式开始执行
### call stack(执行上下文栈)
组织管理程序运行过程中的执行上下文
### 代码演示
```bash
var a = 2;
function bar () {
  var i = 3;
  function foo () {
    var b = 5;
    method();
  }
  foo()
  console.log('1231')
}
function method() {
  console.log('abc')
}
bar()
```
执行栈
```bash 
- console.log 函数的执行上下文
- method 函数的执行上下文
- foo 函数的执行上下文
- bar 函数的执行上下文
- 栈低：全局执行上下文
```

### 执行上下文的内容
1.VO:variable object, 变量对象，存放的是函数或全局代码执行过程中需要用到的局部变量
2.scope: 作用域
3.this

### vo是一个对象，调用函数或执行全局代码是创建，创建一个ov，需要经过三步
* 1.确定参数
* 2.确定函数中所以的函数字面量声明
    1.该函数必须是字面量生命，字面量声明提取到vo后，可以认为该声明失效
    2.如果当前vo出现同名属性，直接覆盖
* 3.确定函数中所有的变量声明（var）, 将其提取到上下文中，值为undefined；
    1.如果当前vo中存在同名属性，忽略

#### 创建vo对象
```bash
{
  vo: {
    a: 3,
    b: 4,
    argument: {....},
    bar: 指定一个函数,
    i: undefined,
    j: undefined,
    k: undefined
  }
}
```

#### 代码
```bash
function foo(a, b) {
  console.log(a, j, bar)
  a(); # 能够调用
  function a() {
    console.log('hello');
  }
  var i = 123;
  var j = 111;
  var k = 321
}
foo(3, 4, 5, 6);
```


