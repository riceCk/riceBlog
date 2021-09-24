---
title: html5-fileReader_websocket-lesson6
categories: Html5老司机必会技能
tags: HTML5
date: 2019-05-21 20:46:50
---

# fileReader 文件读取
## fileReader 方法
通过不同的方式读取文件
* abort() 终止读取
* readAsBinaryString(file) 将文件读取为二进制编码
* readAsDataURL(file) 将文件读取为DataURL编码base64
* readAsText(file, [encoding]) 将文件读取为文本
* readAsArrayBuffer(file) 将文件读取为arraybuffer

## fileReader 事件
* onloadstart 读取开始时触发
* onprogress读取中
* onloadend 读取完成时触发，无论成功或者失败
* onloade 文件读取成功完成时触发
* onabort 中断时触发
* onerror 出错时触发

```bash
let reader = new FileReader();
let inp = document.getElementsByTagName('input')[0];
inp.onchange = function () {
  console.log(inp.files) # 文件信息
  reader.readAsDataURL(inp.files[0])
}    
reader.onloadstart = function (e) {
  console.log(this === e.target) # true
  console.log('start：读取开始时触发', e);
}
reader.onprogress = function (e) {
  console.log('读取中', e)
}
reader.onload = function (e) {
  console.log('成功触发', e)
}
reader.onloadend = function (e) {
  console.log('读取完成无论成功失败', e)
}
```

## 举几个栗子
### 实例图片读取展示
```bash
<input type="file">
<img src="" alt="">
-----------------js---------------------------------
let reader = new FileReader();
let inp = document.getElementsByTagName('input')[0];
let img = document.getElementsByTagName('img')[0];
inp.onchange = function () {
  console.log(inp.files) # 文件信息
  reader.readAsDataURL(inp.files[0])
}    
reader.onloadstart = function (e) {
  console.log(this === e.target) # true
  console.log('start：读取开始时触发', e);
}
reader.onload = function (e) {
  img.src = this.result;
  console.log('成功触发', e)
}
```
### 上传大的文件写进度条,以及终止
```bash
.progress {
  width: 300px;
  height: 30px;
  border: 1px solid #000;
}
.bar {
  width: 0;
  height: 30px;
  background-color: aqua;
}
---------------------css--------------------------------
<input type="file">
<div class="progress">
  <div class="bar"></div>
</div>
<span class="text"></span>
<button class="abort">终止</button>
---------------------html--------------------------------
let reader = new FileReader();
let inp = document.getElementsByTagName('input')[0];
let text = document.getElementsByClassName('text')[0];
let bar = document.getElementsByClassName('bar')[0];
let progress = document.getElementsByClassName('progress')[0];
let abort = document.getElementsByClassName('abort')[0]
abort.onclick = function () {
  reader.abort();
}
inp.onchange = function () {
  console.log(inp.files) # 文件信息
  reader.readAsDataURL(inp.files[0])
}    
reader.onprogress = function (e) {
  console.log('读取中', e)
  let precent = e.loaded / e.total;
  let width = progress.offsetWidth * precent + 'px';
  bar.style.width = width
  text.innerHTML = parseInt(precent * 100) + '%'
}
```

### file分割方法slice,封装一个实例
用于较大文件上传到服务器上，利用分割文件，异步同时上传减少上传时间
```bash
.progress {
  width: 300px;
  height: 30px;
  border: 1px solid #000;
}
.bar {
  width: 0;
  height: 30px;
  background-color: aqua;
}
---------------------css--------------------------------
<input type="file">
<div class="progress">
  <div class="bar"></div>
</div>
<span class="text"></span>
---------------------html--------------------------------
let reader = new FileReader();
let inp = document.getElementsByTagName('input')[0];
let text = document.getElementsByClassName('text')[0];
let bar = document.getElementsByClassName('bar')[0];
let progress = document.getElementsByClassName('progress')[0];
inp.onchange = function () {
  console.log(inp.files) # 文件信息
  const reader = new PartFileReader(inp.files[0], 'readAsBinaryString', {
    loadStart: function (e) {
      console.log('start', e)
    },
    progress: function (e, loaded, total) {
      console.log('progress加载中', e)
      let precent = loaded / total;
      let width = progress.offsetWidth * precent + 'px';
      bar.style.width = width
      text.innerHTML = parseInt(precent * 100) + '%'
    },
    load: function (e) {
      console.log('load成功加载', e);
    },
    loadend: function (e) {
      console.log('end', e)
    },
    abort: function (e) {
      console.log('abort', e)
    }
  })
}
```
封装的**fileReader.js**方法
```bash
# 封装分割文件上传
function PartFileReader(files, type, event) {
  this.files = files; # 文件信息
  this.type = type;   # 文件读取类型
  this.event = event; # 绑定事件
  this.step = 1024 * 1024; # 分割的每块
  this.total = this.files.size;
  this.reader = new FileReader(); # 重置文件
  this.loaded = 0;     # 记录每次上传的进度
  this.abort = this.reader.abort()  # 暂停按钮
  this.readPartFile(0)  # 调用函数
  this.bindEvent();
}
PartFileReader.prototype.readPartFile = function (start) {
  if (this.files.slice) {
    let file = this.files.slice(start, start + this.step)
    console.log(this)
    this.reader[this.type](file);
  }
}
PartFileReader.prototype.bindEvent = function () {
  let self = this
  this.reader.onloadstart = function (e) {
    self.event.loadStart && self.event.loadStart.call(self, e);
  }
  this.reader.onprogress = function (e) {
    self.event.progress && self.event.progress.call(self, e, self.loaded, self.total);
  }
  self.reader.onload = function (e) {
    self.loaded += e.loaded;
    self.event.load && self.event.load.call(self, e);
    if (self.loaded < self.total) {
      self.readPartFile(self.loaded);
    }
  }
  self.reader.onloadend = function (e) {
    self.event.loadend && self.event.loadend.call(self, e);
  }
  self.reader.onabort = function (e) {
    self.event.onabort && self.event.onabort.call(self, e);
  }
}
```

# webSocket 
客户端与服务器都可以主动传送数据给对方;
不用频率创建TCP请求及销毁请求，减少网络带宽资源的占用，同时也节省服务器资源;
* （1）建立在 TCP 协议之上，服务器端的实现比较容易。
* （2）与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
* （3）数据格式比较轻量，性能开销小，通信高效。
* （4）可以发送文本，也可以发送二进制数据。
* （5）没有同源限制，客户端可以与任意服务器通信。
* （6）协议标识符为ws（如果加密为wss）服务器网址就是url
* （7）不受同源策略的限制

## 简单的几个事件
```bash
var socket = new WebSocket('ws://echo.websocket.org/');
  socket.onopen = function (e) {
  socket.send('hello world') # 传数据
}
socket.onmessage = function (e) { # 获取数据
  console.log(e)
  socket.onclose() # 关闭
}
scoket.onclose = function(e) {
  # 监听关闭事件
  console.log('close', socket.readyState);
}
scoket.onerror = function(e) {
  # 连接出错爆发
}
```
## socket.readyState 状态码
![状态码](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/webSocket.png)