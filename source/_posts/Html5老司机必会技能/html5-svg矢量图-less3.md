---
title: html5-svg矢量图-lesson3
categories: Html5老司机必会技能
tags: HTML5
date: 2019-05-05 20:58:03
---
# svg标签使用
## 1.直线
```bash
------------------style---------------------------
svg {
  border: 1px solid #000;
}
line {
  stroke: red; # 填充
  stroke-width: 10px; # 线粗度
}
-------------------html----------------------------
<svg width='500px' height='500px'>
  <line x1='100' y1='100' x2='200' y2='100'></line>
  # (x1, y1) 起始坐标，(x2, y2) 终点坐标
</svg>
```
## 2.矩形
矩形rect自动填充，不需要在style中`stroke`
```bash
<svg width='500px' height='500px'>
  <rect x='50' y='50' width='100' height='100' rx='10' ry='20'></rect>
  # (x, y) 起始坐标, (rx, ry) 矩形四角弧度
</svg>
```
## 3.圆
```bash
<svg width='500px' height='500px'>
  <circle r='50' cx='220' cy='100'></circle>
  # r半径，（cx, cy）圆心坐标
</svg>
```
## 4.椭圆
```bash
<svg width='500px' height='500px'>
  <ellipse rx="100" ry="50" cx="400" cy="150"></ellipse>
  # rx: x轴半径，ry：y轴半径， (cx, cy) 椭圆圆心
</svg>
```
## 5.折线
```bash
------------------------style----------------------------------
polyline {
  fill: transparent;
  stroke: red;
}
------------------------html------------------------------------
<svg width='500px' height='500px'>
  <polyline points="100 100, 200 50, 300 100, 400 50"></polyline>
</svg>
```
## 6.多边形
```bash
<svg width='500px' height='500px'>
  <polygon points="125 125, 130 140, 120 140"></polygon>
</svg>
```
## 7.文本
```bash
<svg width='500px' height='500px'>
  <text x='125' y='220'>hello world</text>
</svg>
```
# svg样式属性
* fill 填充
* stroke 描边
* stroke-width 描边粗细
* stroke-opacity/fill-opacity 透明度
* stroke-linecap 线段加不加帽子
butt（默认）、round（圆）、square（正方形）
* stroke-linejoin 角度样式（尖还是平）
bevel（切掉没有尖）、round（圆）、miter（默认）
* stroke-miterlimit: 30; 不写像素，判断stroke-linejoin：miter是尖还是平

# Path 元素
## 线段图形
### 1.M指令和L指令
大写ML的时候相对与画布的坐标，表示绝对坐标
```bash
<svg width='500px' height='500px'>
  <path d="M 100 100 L 200 100 L 200 200"></path>
</svg>
```
### 2.m指令和l指令
小写ml的时候相对于上个点的像素坐标，表示相对坐标
### H和V命令，h和v命令
```bash
# 相对于(100, 100)坐标(200, 100).(200, 300)
<path d="M 100 100 H 200 V 300"></path>
# 相对于(100, 100)坐标(300, 100)(300, 400) z闭合
<path d="M 100 100 h 200 v 300 z"></path>
```
### 3.Z指令
闭合不区分大小写
## 圆弧指令
### 4.A命令（elliptical Arc）七个参数
(rx ry x-axis-rotation large-arc-flag sweep-flag x y)
* rx ry 圆弧的x轴半径和y轴半径
* x-axis-rotation 圆弧相对x轴的顺转角度，默认的顺时针，可以设置负值
* large-arc-flag 表示圆弧路径的是大圆弧还是小圆弧，1大圆弧
* sweep-flag表示从起点到终点是顺时针还是逆时针，1表示顺时针，0表示逆时针
* x y 表示终点坐标，绝对或相对

```bash
<svg width='500px' height='500px'>
  <path d="M 100 100 A 70 120 0 1 1 150 200"></path>
</svg>
```
## 贝塞尔曲线
### 二次贝塞尔曲线
#### 最基础的二次贝塞尔曲线
```bash
<svg width='500px' height='500px'>
  <path d="M 100 100 Q 200 50 300 300"></path>
</svg>
```
![二次贝塞尔曲线](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/two.png)
#### 扩展点的二次贝塞尔曲线T
T会做(300,300)为中点(200, 50)和(x,y)延长线扩展的点
```bash
<svg width='500px' height='500px'>
  <path d="M 100 100 Q 200 50 300 300 T 350 200"></path>
</svg>
```
![二次贝塞尔曲线扩展T](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/twoT.png)

# svg渐变
## 1.线性渐变
```bash
<svg width='500px' height='500px'>
  <defs>
    <linearGradient id="bg1" x1="0" y1="0" x2="0" y2="100%">
      <stop offset="0%" style="stop-color: rgb(255, 255, 0)"></stop>
      <stop offset="100%" style="stop-color: rgb(255, 0, 0);"></stop>
    </linearGradient>
  </defs>
  <rect x='100' y='100' width="200" height='200' style="fill:url(#bg1)"></rect>
</svg>
```
## 2.径向渐变radialGradient
(cx,cy)圆心，r半径（50%矩形的一半）,(fx, fx)径向圆的位于矩形的位置（50%中间）
```bash
<svg width='500px' height='500px'>
  <defs>
    <linearGradient id="bg1" x1="0" y1="0" x2="0" y2="100%">
      <stop offset="0%" style="stop-color: rgb(255, 255, 0)"></stop>
      <stop offset="100%" style="stop-color: rgb(255, 0, 0);"></stop>
    </linearGradient>
    <radialGradient id="bg2" cx="50%" cy="50%" r="50%" fx="50%" fy="50%">
      <stop offset="0%" style="stop-color: green"></stop>
      <stop offset="100%" style="stop-color: red"></stop>
    </radialGradient>
  </defs>
  
  <rect x='100' y='100' width="200" height='200' style="fill:url(#bg2)"></rect>
</svg>
```

## 3.高斯滤镜
```bash
<svg width='500px' height='500px'>
  <defs>
    <filter id="Gaussian_Blur">
      <feGaussianBlur in="SourceGraphic" stdDeviation="20" />
    </filter>
  </defs>
  
  <rect x='100' y='100' width="200" height='200' style="fill:url(#bg2);filter:url(#Gaussian_Blur)" ></rect>
</svg>
```

# svg路径样式图
## stroke-dasharray样式切割
```bash
line {
  stroke: red;
  stroke-width: 10px;
  stroke-linecap: butt;
  stroke-dasharray: 10px;
}
---------------------style------------------------
<svg width='500px' height='500px'>
  <line x1="100" y1="100" x2="100" y2="200"></line>
</svg>
```
![路径](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/line-1.jpg)
```bash
line {
  stroke: red;
  stroke-width: 10px;
  stroke-linecap: butt;
  stroke-dasharray: 10px 20px;
}
```
![路径](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/line-2.png)
```bash
line {
  stroke: red;
  stroke-width: 10px;
  stroke-linecap: butt;
  stroke-dasharray: 10px 20px 30px;
}
```
![路径](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/line-3.png)

## stroke-dashoffset移动的像素
```bash
line {
  stroke: red;
  stroke-width: 10px;
  stroke-linecap: butt;
  stroke-dasharray: 10px;
  stroke-dashoffset: 300px;
}
---------------------style------------------------
<svg width='500px' height='500px'>
  <line x1="100" y1="100" x2="100" y2="200"></line>
</svg>
```
## 实例抽风效果
![抽风路径](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/stroke.gif)

```bash
line {
  stroke: red;
  stroke-width: 10px;
  stroke-linecap: butt;
  stroke-dasharray: 300px;
  stroke-dashoffset: 300px;
  animation: move 2s linear infinite alternate-reverse;
}
@keyframes move {
  0% {
    stroke-dashoffset: 300px;
  }
  100% {
    stroke-dashoffset: 0px;
  }
}
```
# js 操作svg
* 1.getTotalLength 获取路径总长度
* 2.getPointAtLength(x) 获取路径上距离起始点距离x长度的点的坐标
```bash
<svg width='300px' height='300px'>
  <line x1="100" y1="100" x2="500" y2="100"></line>
</svg>
------------------------------js------------------------
let svg = document.getElementsByTagName('svg')[0];
let line = document.getElementsByTagName('line')[0];
console.log(line.getTotalLength()); # 线段两点之间的长度 400
console.log(line.getPointAtLength(10)) # SVGPoint {x: 110, y: 100}
```
## js创建svg
```bash
let svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
let line = document.createElementNS('http://www.w3.org/2000/svg', 'line');
svg.setAttribute("width", 200);
svg.setAttribute("hieght", 200);
line.setAttribute('x1', 100);
line.setAttribute('y1', 100);
line.setAttribute('x2', 150);
line.setAttribute('y2', 150);
svg.appendChild(line);
document.body.appendChild(svg);
```
# ViewBox 表示可视区盒子
```bash
<svg width='300px' height='300px' viewBox="0,0,300,300"></svg>
```
## preserveAspectRatio
第一个值又是由两部分组成，前半部分表示x方向对齐，后半部分表示y方向对齐。meet：保持纵横比缩放viewBox适应viewport，slice保持纵横比同时比例小方向放大填满viewport，none：扭曲纵横比以充分适应viewport
```bash
<svg width='300px' height='300px' viewBox="0,0,300,200" preserveAspectRatio="xMinYMin meet">
```
|值|含义|
|:---:|:----:|
|xMin|viewport和viewBox左边对齐|
|xMid|viewport和viewBox中间对齐|
|xMax|viewport和viewBox右边对齐|
|YMin|viewport和viewBox上边对齐|
|YMid|viewport和viewBox中间对齐|
|YMax|viewport和viewBox下边对齐|

# canvas与svg区别
||canvas|svg|
|:---:|:----:|:---:|
|操作对象|基于像素（动态点阵图）|基于图形元素|
|元素|单个html元素|多种图形元素（rect、path、line....）|
|驱动|只能脚本驱动|支持脚本和css|
|事件交互|用户交互到像素点(x,y)|用户交互到图形元素(rect,path)|
|性能|适合小面积，大数量应用场景|适合大面积，小数量应用场景|