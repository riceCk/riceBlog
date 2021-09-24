---
title: javascript必备常用知识
categories: javascript
tags: JavaScript
date: 2018-01-15 09:10:00
---
# 浏览器组成
* 用户界面
* 浏览器引擎
* 渲染引擎
* 网络
* UI后端
* js引擎
* 数据存储 

# 渲染引擎
## 渲染
在电脑绘图中是指软件从模型生成图像的过程
## 渲染引擎
其职责就是渲染，即在浏览器窗口中显示所有请求的内容
## 过程
解析html从而构建DOM树->CSS Rule 树-> 布局Render树 -> 绘制Render树
# 渲染模式
```bash
<!DOCTYPE html>
```  
# label 标签
label和input关联运用
```bash
<p>
  <label for="demo">username</label>
  <input type="text" id="demo">
</p>
```
# 属性和特性
属性包含了特性，属性属于大类
特性：DOM对象一一对应，映射关系
## setAttribute和getAttribute
```bash
oInput.setAttribute('log', 'aaa');
oInput.getAttribute('log')
```
# 图片预加载、懒加载

## 图片预加载的实例应用
```bash
var oImg = new Image();
oImg.onload = function(){
  var oDiv = document.getElementById('demo');
  oDiv.appendChild(this);
}
oImg.src='xxxxx';   
```
## 懒加载配合预加载

```bash
function check(){
  if(oDiv.offsetTop <= window.pageYOffset + window.innerHeight){
    var oImg = new Image();
    oImg.onload = function(){
      var oDiv = document.getElementById('demo');
      oDiv.appendChild(this);
    }
    oImg.src='xxxxx'; 
  }
}
window.onscroll = function(){
    check();
} 
```
# Math.random();
随机数范围[0, 1),如果随机去一个范围，[12, 36)
36 - 12 = 24
24 * Math.random()  => [0, 24);
12 + 24 * Math.randow()  => [12, 36);

# 文档碎片
创建文档碎片，避免li频繁创建插入dom，导致重排重绘，影响性能
```bash
var oF = document.createDocumentFragment();
var oUl = document.getElementById('wrapper');
for(var i = 0; i < 10; i++){
  var newLi = document.createElement('li');
  newLi.innerText = i + '';
  oF.appendChild(newLi);
}
oUl.appendChild(oF);
```
# className封装
```bash
Element.prototype.getElementsByClassName = Document.prototype.getElementsByClassName = function (_className){
# _className
#获取document下面的所有标签
var allDomArray = document.getElementsByTagName('*')
var lastDomArray = [];
# 正则去掉class类多余空格
function trimSpace (strClass){
  var reg = /\s+/g;
  return strClass.replace(reg, ' ')    
}
Array.from(allDomArray, item =>{
  #trim()此方法用于去掉字符串首位空格，封装好的现成方法
  var lastStrClass = trimSpace(item.className).trim();
  var classArray = lastStrClass.split(' ');
  classArray.forEach(ele => {
    if(ele === _className){
      lastDomArray.push(item)
    }
  })
})
return lastDomArray;
}
console.log(document.getElementsByClassName('demo'))
```
# 断掉调试
debugger