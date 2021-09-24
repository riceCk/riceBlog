---
title: BOM
categories: javascript
tags: JavaScript
date: 2018-01-14 09:00:00
---

# 什么是BOM？
BOM是browser object model的缩写，简称浏览器对象模型
主要处理浏览器窗口（window）和框架（ifarme），描述了与浏览器进行交互的方法和接口，可以对浏览器窗口进行访问和操作，不过通常浏览器特定的JavaScript扩展都被看做BOM的一部分，扩展如：
* 弹出新的浏览器窗口
* 移动、关闭浏览器窗口以及调整窗口的大小
* 提供Web浏览器详细信息的定位对象
* 提供用户屏幕分辨率详细信息的屏幕对象
* 对cookie的支持
* IE扩展了BOM，加入ActiveXObject类，可以通过JavaScript实例化ActiveX对象

# BOM的组成
* Window JavaScript层级中的顶层对象表示浏览器窗口
* Navigator包含客户端浏览器的信息
* History 包含了浏览器窗口访问过的URL
* Location包含了当前URL的信息
* Screen包含客户端显示屏的信息

## window中的属性
Window 对象表示一个浏览器窗口或一个框架。在客户端 JavaScript 中，Window 对象 是全局对象，所有的表达式都在当前的环境中计算。也就是说，要引用当前窗口根本不需要 特殊的语法，可以把那个窗口的属性作为全局变量来使用。例如，可以只写 document，而 不必写 window.document。

### offsetTop、pageYOffset、innerHeight实现懒加载
* offsetTop 关于页面div最顶部距离
* window.pageYOffset 滚轮滚动距离
* window.innerHeight 可视窗口的大小
```bash
function check(_id){
  var oDiv = document.getElementById('demo');
  if(oDiv.offsetTop <= window.pageYOffset + window.innerHeight){
    setInterval(function(){
      if(oDiv.style.opacity == '1'){
        clearInterval(oDiv.timer);
      }else{
        oDiv.style.opacity = parseFloat(oDiv.style.opacity) + 0.01;
      }
    }, 30)
  }
}
window.onscroll = function(){
    check('demo');
} 
```
### screenX、screenTop
浏览器距离显示屏的距离
```bash
window.screenX || window.screenLeft
window.screenY || window.screenTop
```
### top、self及parent
```bash
#iframe 实现跨域
<iframe src="" frameborder="0"></iframe>
# 父级 a = 10;
# 子级
window.parent.a  #=> 10
# 对底层获取最顶部的数据
window.top.a     #=> 10
```
### window.name 给窗口去一个名字
### window.confirm 确定取消弹框
```bash
window.confirm('你确定要关闭我吗')
window.prompt('请输入你的姓名')
# 可以有输入框，获取输入框的值
```
### open、close
```bash
window.open('http://www.baidu.com', 'baidu', 'width=200,height=200')
# 打开一个新窗口
window.close()
# 关闭当前窗口
```
## Navigator对象属性
Navigator 对象包含的属性描述了正在使用的浏览器。可以使用这些属性进行平台专用的配置。虽然这个对象的 名称显而易见的是 Netscape 的 Navigator 浏览器，但其他实现了 JavaScript 的浏览器也支持这个对象
### navigator.onLine 脱机状态
### navigator.cookieEnabled
返回指明浏览器中是否启用 cookie 的布尔值。
### userAgent
返回由客户机发送服务器的 user-agent 头部的值

## History 对象
History 对象包含用户（在浏览器窗口中）访问过的 URL
### history.length
返回浏览器历史列表中的 URL 数量
### history.back()
加载 history 列表中的前一个 URL
### history.forward()
加载 history 列表中的下一个 URL
### history.go(2)
加载 history 列表中的某个具体页面

## Location 对象
Location 对象包含有关当前 URL 的信息
### location.protocol 
设置或返回当前 URL 的协议：
`https:`、`http:`
### location.host
设置或返回主机名和当前 URL 的端口号:
`www.baidu.com`
### location.pathname
设置或返回当前 URL 的路径部分:
`/s`
### location.search
设置或返回从问号 (?) 开始的 URL（查询部分）:
`?wd=nbw&rsv_spt=1&rsv_iqid=0xfc2ab6300001d70e&issp=1&f=8&rsv_bp=1&rsv_idx=2&ie=utf-8&rqlang=cn&tn=baiduhome_pg&rsv_enter=0&oq=nbw&rsv_t=cb3fUOZ%2FooHMIwdUt0yuStlpT5k5AobI4LRpp9S04sdiCcG4qJkTiNJ3wZ8%2F5c%2FyDXMS&rsv_pq=cf2743530001c37b`
### location.hash
设置或返回从井号 (#) 开始的 URL（锚）:
`#10`
### location.href = 'https://www.taobao.com'
设置或返回完整的 URL