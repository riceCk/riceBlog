---
title: html5-动画优化_客户端存储_历史记录_worker-lesson5
categories: Html5老司机必会技能
tags: HTML5
date: 2019-05-20 15:34:50
---
# 动画优化
## requestAnimationFrame与cancelAnimationFrame
* 页面刷新前执行一次
* 1000ms 60fps -> 16ms
* cancelAnimationFrame 清楚动画
* 用法与setTimeout类似

```bash
let demo = document.getElementsByClassName('demo')[0];
function move() {
  demo.style.left = demo.offsetLeft + 50 + 'px';
  let timer = requestAnimationFrame(move); 
  # 设置动画
  if (demo.offsetLeft > 800) {
    cancelAnimationFrame(timer);
    #停止动画
  }
}
move()
```
## requestAnimationFrame 重写方式
```bash
window.requestAnimationFrame = (function () {
  return window.requestAnimationFrame 
      || window.webkitRequestAnimationFrame 
      || window.mozRequestAnimationFrame 
      || function (callback) {
        window.setTimeout(callback, 1000 / 60);
      }
})()
```
## cancelAnimationFrame 重写方式
```bash
window.cancelAnimationFrame = (function () {
  return window.cancelAnimationFrame 
      || window.webkitCancelAnimationFrame 
      || window.mozCancelAnimationFrame
      || function (id) {
        window.clearTimeout(id);
      }
})()
```
# 客户端存储
## storage
* localStorage
* sessionStorage

### 区别
cookie 存储量4k，localStorage/sessionStorage大概5M
* localStorage -> 永久的，除非手动删除
* sessionStorage -> 临时的，窗口关闭就没有了
* localStorage -> 作用域文档源限制
* sessionStorage -> 文档源限制+窗口

### localStorage
```bash
# 赋值
localStorage.name = 'riceCk'
localStorage.arr = [1, 2, 3]
localStorage.obj = JSON.stringify({
  name: 'riceCk'
})
# 取值
localStorage.name 
localStorage.arr
JSON.parse(localStorage.obj)
```

### localStorage/sessionStorage的api
api用法一样以localStorage为例
* localStorage.setItem('age', 18) 设置属性
* localStorage.getItem('age') 获取属性
* localStorage.removeItem('age') 删除属性
* localStorage.clear() 清空所有属性

## cookie
* `document.cookie = 'name=amind'` 设置cookie
* document.cookie 获取cookie
* encodeURIComponent('陈凯') 编码
"%E9%99%88%E5%87%AF"
* decodeURIComponent("%E9%99%88%E5%87%AF")
转码

### cookie分割函数
```bash
function getCookie(name) {
  let name = name + '=';
  let ca = document.cookie.split(';');
  for(let i = 0; i < ca.length; i++) {
    let c = ca[i];
    while(c.charAt(0) == '') c == c.substring(1);
    if (c.indexOf(name) != -1) return c.substring(name.length, c.length)
  }
  return '';
}
```

## cookie和storage区别
![cookie和storage区别](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/cookie_storage.png)

# 历史记录
* history.back() 加载历史列表中的前一个 URL（如果存在）
* history.forward() 加载历史列表中的后一个 URL（如果存在）
* history.go(n) 跳转历史记录，负数回退，正数前进

## html5新增的方法
### pushState 添加历史记录
用于单页面操作
```bash
history.pushState({}, null, 'demo.htlm')
# 跳转到demo.html,必须要同源域下
```
### replaceState 替换当前历史记录
```bash
history.replaceState({}, null, 'demo.htlm')
```

# worker
必须在服务器下还有效,必须满足同源策略
## 主线程和worker线程通信
**传参：** a.postMessage(num);
**接收参数：** 监听时间a.onmessage = (e) => console.log(e.data)
**终端辞退worker：** a.terminate()
**工人自己辞职：**this.close()
```bash
-----------------主页面index.html---------------
let num = 100000;
let a = new Worker('client.js'); # 定义worker
a.postMessage(num); # 传参
console.log('----------')
a.onmessage = function (e) {
  console.log(e.data, 'index')
  a.terminate() # 终端
}
------------------client.js----------------------
this.onmessage = function (e) { # 获取参数
  console.log(e)
  let sum = 0;
  for (let i = 0; i < e.data; i++) {
    sum += i;
  }
  console.log(sum)
  this.postMessage({
    sum: sum
  })
  this.close()
}
```
## worker缺点
* 受同源策略的限制
* 受主线程DOM的限制
* Worker线程和主线程不在同一个上下文环境，他们不能直接通信，必须通过消息完成
* 脚本限制：worker线程不能执行alert或者confirm方法但可以使用HMLHTTPRequest对象发出ajax请求
* 文件限制：worker线程无法读取本地文件，既不能打开本机的系统，他说加载的脚本必须来自网络