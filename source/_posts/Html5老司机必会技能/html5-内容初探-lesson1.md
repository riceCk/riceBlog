---
title: html5-内容初探-lesson1
categories: Html5老司机必会技能
tags: HTML5
date: 2019-04-19 10:45:03
---
# HTML5新增标签
header、footer、article（div）、section（p）、nav、aside

# HTML5新增属性
## 1.contenteditable 用户是否修改页面上的内容
```bash
# 鼠标点击可以修改
<div contenteditable="true"></div>
```
## 2.draggable 支持拖放
```bash
<div draggable='true'>123</div>
```
### 元素本身的拖拽事件
```bash
<div draggable='true' id='demo'></div>
-------------------------------------------
let demo = document.getElementById('demo');
demo.ondragstart = function (e) {
  console.log('start开始', e)
}
demo.ondrag = function (e) {
  console.log('drag拖拽过程', e)
}
demo.ondragend = function (e) {
  console.log('end鼠标放下', e)
}
```
### 对元素拖拽区域事件
```bash
<div draggable='true' id='demo'></div>
<div class="wrapper"></div> # 拖拽区域
---------------------------------------
let wrapper = document.getElementsByClassName('wrapper')[0];
wrapper.ondragenter = function (e) {
  console.log('enter-拖拽物进入区域');
}
wrapper.ondragover = function (e) {
  e.preventDefault(); # 取消默认事件
  console.log('over-拖拽物在区域移动');
}
wrapper.ondragleave = function (e) {
  console.log('leave-拖拽物离开区域');
}
wrapper.ondrop = function (e) {
  console.log('drop-拖拽物放在区域')
}
```
### 小实例,拖拽小木块到指定区域中
```bash
-------------------html------------------------
<div draggable='true' id='demo'></div>
<div class="wrapper"></div>
-------------------js--------------------------
let demo = document.getElementById('demo');
let wrapper = document.getElementsByClassName('wrapper')[0];
let id = null;
demo.ondragstart = function (e) {
  console.log('start', e)
  id = this.id
}
wrapper.ondragenter = function (e) {
  console.log('enter-拖拽物进入区域');
}
wrapper.ondragover = function (e) {
  e.preventDefault(); # 取消默认事件
  console.log('over-拖拽物在区域移动');
}
wrapper.ondragleave = function (e) {
  console.log('leave-拖拽物离开区域');
}
wrapper.ondrop = function (e) {
  console.log('drop-拖拽物放在区域')
  # cloneNode(true)克隆目标元素 
  let oDiv = document.getElementById(id).cloneNode(true)
  this.appendChild(oDiv);
}
```
### 数据传输过程中对象dataTransfer对象
返回用于传输数据到释放区的对象
* e.dataTransfer.setData 存储数据
* e.dataTransfer.getData 获取数据
* e.dataTransfer.types 获取存储的数据类型
* e.dataTransfer.files 拖动外部文件的触发获取当前文件属性（drop事件触发）

```bash
let demo = document.getElementsByClassName('demoWrapper')[0];
let wrapper = document.getElementsByClassName('wrapper')[0];
let id = null;
demo.ondragstart = function (e) {
  console.log('start', e)
  # 存储传递的数据
  e.dataTransfer.setData('id', e.target.id); 
  # 传obj对象
  e.dataTransfer.setData('obj', JSON.stringify({
    id: e.target.id
  }))
}
wrapper.ondragover = function (e) {
  e.preventDefault();
  console.log('over-拖拽物在区域移动');
}
wrapper.ondrop = function (e) {
  console.log('drop-拖拽物放在区域', e)
  console.log(e.dataTransfer.getData('id'),1231);
  # JSON.parse(e.dataTransfer.getData('obj'));
  # 提取传递的数据
  console.log(e.dataTransfer.files) # 获取当前文件
  let id = e.dataTransfer.getData('id');
  let oDiv = document.getElementById(id).cloneNode(true)
  this.appendChild(oDiv);
}
```

## 3.hidden 隐藏
```bash
<div hidden></div>
```
## 4.contextmenu 为元素设定快捷菜单
目前无浏览器支持该属性
