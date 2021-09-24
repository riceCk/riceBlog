---
title: 浏览器从请求到页面渲染的各个细节
categories: 面试题
tags: 面试题
date: 2018-11-10
---
# 从渲染到虚拟Dom 
```bash
问题：
1.响应时间 0~1000
2.动画     16ms
3.加载时间 
JavaScript setTimeout/setInterval 操作DOM
CSS
layout 布局 在页面中DOM元素的大小，以及位置, rflow(联动）
paint 绘图 元素颜色，图片，文本
composite  渲染成合并
解决优化：
1、优化JavaScript执行效率
requestAnimationFrame (h5代替定时器)
workers 新建一个线程不会堵塞主线程的运行，优化数据庞大的计算
对于css选择层级不要过深（最好不要超过5层）
2、布局：使用流式布局 flex
3、paint重绘，
```
# 页面请求到渲染的基本流程
url地址分成协议://域名（端口）

* 用户输入url地址
* 浏览器解析url解析出主机名
* 浏览器将主机名转换成服务器ip地址（浏览器先查找本地DNS缓存列表，没有的话，在想浏览器默认的DNS服务器发送查询请求同时缓存）
* 浏览器将端口号从url中解析出来
* 浏览器建立一条与目标Web服务器的TCP连接（三次挥手）
* 浏览器向服务器发送一条HTTP请求报文
* 服务器向浏览器返回一条HTTP响应报文
* 关闭连接（四次挥挥手），浏览器解析文档
* 如果文档有资源重复6 7 8动作直至资源全部加载完毕

## 浏览器渲染主要流程
* html解析出DOM Tree
* css解析成Style Tree
* 将两者关联成Render Tree
* Layout（布局）根据Render Tree计算每个节点的信息（宽高）
* Painting（绘制）根据计算好的信息绘制整个页面

## 浏览器的高级结构
* 用户界面：这包括地址栏，后退/前进按钮，书签菜单等。浏览器的每个部分都显示除了您看到所请求页面的窗口。
* 浏览器引擎：在UI和呈现引擎之间编组操作。
* 渲染引擎：负责显示请求的内容。例如，如果请求的内容是HTML，则呈现引擎解析HTML和CSS，并在屏幕上显示解析的内容。
* 网络：用于网络调用，例如HTTP请求，在独立于平台的界面后面使用不同平台的不同实现。
* ui后端：用于绘制组合框和窗口等基本小部件。此后端公开了一个非平台特定的通用接口。它下面使用操作系统用户界面方法
* JS引擎：用于解析和执行JavaScript代码
* 数据结构：这是一个持久层。浏览器可能需要在本地保存各种数据，例如cookie。浏览器还支持存储机制，例如localStorage，IndexedDB，WebSQL和FileSystem。

## 重排（reflow）成本比较高，如：
* 当你增加、删除、修改DOM结点时，会导致Reflow或者Repaint（重排重绘）
* 当你移动Dom的位置，或是高出动画的时候（建议requestAnimationFrame代替定时器）
* 当你修改Css样式的时候
* 当你Resize（调整）窗口的时候，或是滚动的时候
* 当你修改网页默认字体时
注：display：none会触发reflow，而visibility只会触发repaint，因为位置没有变化
```bash
$('body').css('color': 'red')  // repaint
$('body').css('margin', '2px') // reflow, repaint
let bstyle =document.body.style  // 建立节点
bstyle.padding = '20px'  // reflow, repaint
bstyle.border = '10px solid red' // 再一次reflow 和 repaint
bstyle.color = 'blue'   // repaint
bstyle.backgroundColor = '#fad'  // repaint
bstyle.fontSize = '2em'  // reflow, repaint
```
## 优化CSS重排重绘与浏览器性能
### 什么时候会导致reflow发生呢？
* 改变窗口大小
* 改变文字大小
* 增加/删除样式表
* 内容的改变（用户在输入框中写入内容也会）
* 激活伪类，如hover
* 操作class属性
* 脚本操作DOM
* 计算offsetWidth和offsetHeight等
* 设置style属性

| 常见的重排元素 |     |    |     |
|:--------:|:--------:|:--------:|:--------:|
|width|height|padding|margin|
|display|border-width|border|top|
|position|font-size|float|text-align|
|overflow-y|font-weight|overflow|left|
|font-family|line-height|vertical-align|right|
|clear|white-space|bottom|min-height|
#### 减少reflow对性能的影响建议
* 不要一条一条的修改Dom样式，预先定义好class，然后修改Dom的**className**
* 不要Dom离线后修改，比如：先把Dom给**display:none**（又一次Reflow），然后你修改100次，在然后再把他显示出来
* 不要把Dom节点的属性值放在一个循环里当成循环里的变量
* 尽可能不要修改影响范围较大的Dom
* 为动画的元素使用绝对定位**absolute/fixed**
* 不要使用**table**布局，可能很小的一个小改动造成整个table重新布局
* 尽可能限制reflow的影响范围，尽可能的在底层Dom节点上，上述例子中，如果你要改变p的样式，class就不要加在div上，通过父元素去影响子元素不好
* 避免设置大量的style属性，因为通过设置style属性改变节点样式的话，每一次设置都会触发一次reflow，所以，最好是使用class属性
* 实现元素的动画，它的position属性，最好是设为absoulte或fixed，这样不会影响其他元素的布局
* 动画实现的速度的选择。比如实现一个动画，以1像素为单位移动这样最平滑，但是reflow就会过于频繁，大量消耗CPU资源，如果以3个像素为单位移动则会好很多
* 不要使用table布局，因为table中某一个元素一旦触发了reflow，那么整个table的元素都会触发reflow。那么在不得已使用table的场合，可以设置**table-layout:auto**或者是**table-layout：fixed**这样可以让table一行一行的渲染，这种做法也是为了限制reflow的影响范围
* 如果css里面有计算表达式，每次都会重新计算一遍，触发一次reflow

### 什么是repaint
repaint是在一个元素的外观被改变，但没有改变布局的情况下发生的，如改变了visibility、outline、background等。当repaint发生时，浏览器会验证DOM树上所有其他节点的visibility属性。
#### 避免过分重绘(Repaints)
当元素改变的时候，将不会影响元素在页面当中的位置（比如 **background-color**, **border-color**, **visibility**），浏览器仅仅会应用新的样式重绘此元素，此过程称为 **Repaint**。

|常见的重绘元素|     |       |       |
|:--------:|:--------:|:--------:|:--------:|
|color|border-style|visibility|background|
|text-decoration|background-image|background-position|background-repeat|
|outline-color|outline|outline-style|border-radius|
|outline-width|box-shadow|background-size|   | 

#### 优化动画
css3 动画是优化的重中之重。除了做到上面两点，减少 Reflow 和 Repaints 之外，还需要注意以下方面。

### 启用 GPU 硬件加速

**GPU**（Graphics Processing Unit） 是**图像处理器**。**GPU** 硬件加速是指应用 **GPU** 的图形性能对浏览器中的一些图形操作交给 **GPU** 来完成，因为 GPU **是专门为处理图形而设计**，所以它在**速度和能耗**上更有效率。
**GPU** 加速可以不仅应用于3D，而且也可以应用于2D。这里， **GPU** 加速通常包括以下几个部分：**Canvas2D**，**布局合成（Layout Compositing）**, **CSS3转换（transitions）**，**CSS3 3D变换（transforms）**，**WebGL**和**视频(video)**。

**参考文献：**
[优化CSS重排重绘与浏览器性能](http://caibaojian.com/css-reflow-repaint.html)