---
title: 大厂面试
categories: 面试题
tags: 面试题
date: 2018-10-29 09:48:00
---

# 一、this指向
- 1、全局环境当中this指向全局对象
- 2、函数中的this，由调用函数方式决定
  -1.如果函数时独立调用，在严格模式下是禁止this指向全局对象，而是指向undefined，在非严格模式下，this会指向window
  -2.如果函数时被某一个对象调用，那么this指向被调用的这个对象

# 二、性能优化
- 1、页面级优化
- 2、代码级别的优化

## 页面级优化
- 1、CSS script
- 2、使用CND
- 3、压缩合成代码
- 4、使用DNS预解析 
## 代码级别的优化
- 1、减少DOM操作
- 2、异步加载
- 3、事件代理
- 4、使用reuqestAnimtionFrame来替代setTimout和setInterral
- 5、图片懒加载
 