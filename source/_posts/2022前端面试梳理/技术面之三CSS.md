---
title: 前端随笔之三CSS
categories: 2022前端随笔汇总
tags: 前端随笔
date: 2022-04-28 09:48:00
---

## Css面试题
### 选择器的权重
> * 10000: !important
> * 1000: 内联样式，外联样式
> * 100: ID选择器
> * 10: 类选择器、伪类选择器、属性选择器
> * 1: 标签选择器、伪元素选择器
> * 0：通配选择器、兄弟、父子选择器


### 盒子模型的宽度如何计算
W3C：width = content
IE： width = content+padding+border (横向相关)

### margin纵向重叠问题
```base
# 如下代码：AAA 和 BBB 之间的距离是多少？
<style>
p {
    font-size: 16px;
    line-height: 1;
    margin-top: 10px;
    margin-bottom: 15px;
}
</style>

<p>AAA</p>
<p></p>
<p></p>
<p></p>
<p>BBB</p>
```
* 相邻元素的margin-top 和 margin-bottom会发生重叠
* 空白内容的`<p></p>`也会重叠（相当于忽略不计）
* 答案：15px

### margin 负值问题
对margin的top、left、right、bottom设置负值，有什么效果？
反方向移动p

### BFC理解和应用
* Block format context, 块级格式化上下文
* 一块独立渲染区域，内部元素的渲染不会影响边界以外的元素

#### 形成BFC的常见条件
* float 不是none
* position 是absolute或者fixed
* overflow不是visible
* display是flex inline-block等

#### 应用场景
**1、兄弟结构元素垂直方向的margin重叠**
```bash
#css
.wrapper{
  width: 100%;
  background-color: red;
  overflow: hidden;
}
p{
  margin: 5px auto 25px;
  background-color: pink;
}
#html
<div class="wrapper">
  <p>1</p>
  <p>2</p>
  <p>3</p>
</div>
```
**兄弟元素中margin取最大的25px作为最终渲染**
![样式示意图](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/margin.jpg)


**2、父子级中垂直方向的margin子级会和父级元素结合在一起取最大值（塌陷）**
```bash
#css
.wrapper{
  width: 100px;
  height: 100px;
  background-color: red;
  margin-top: 100px;
}
.content{
  width: 100px;
  height: 50px;
  margin-top: 110px;
  background-color: gray;
}
#html
<div class="wrapper">
  <div class="content"></div>
</div>
```
![塌陷](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/margin-top.png)


### float布局的问题，以及clearfix

#### 圣杯布局和双飞翼布局的目的
* 三栏布局，中间一栏最先加载和渲染（内容重要）
* 两侧内容固定，中间内容随着宽度自适应
* 一般用于PC网页

#### 圣杯布局和双飞翼布局的技术总结
* 使用float布局
* 两侧使用margin负值，以便和中间内容横向重叠
* 防止中间内容被两侧覆盖，一个用padding一个用margin

### flex画筛子
* flex-direction
* justify-content
* align-items
* flex-wrap
* align-self

### flex 属性
* flex-grow 一个数字，进行扩展
* flex-shrink 一个数字，进行收缩
* flex-basis 长度，合法值："auto","%","px","em",或者任意长度的数据

### 定位
#### absolute 和 relative 分别依据什么定位
* relative 依据自身定位
* absolute 依据最近一层定位元素

#### 定位元素
* absoulte relative fixed
* body

#### 水平居中
* inline元素：text-align:center
* block元素：margin: auto;
* absolute元素：left:50% + margin-left负值

#### 垂直居中
* inline元素：line-height的值等于height值
* absolute元素：top：50% + margin-top负值
* absoulte元素：transform(-50%, -50%)
* absoulte元素：top,left,right,bottom = 0;margin: auto

### line-height如何继承
* 写具体数值，如30px，则继承该值
* 写比例，如2/1.5,则继承改比例
* 写百分比，如200%,则继承计算出来的值


### 响应式
#### rem是个长度单位
* px，绝对长度单位，最常用
* em，相对长度单位，相对于父元素，不常用
* rem，相对长度单位，相对于根元素（如：font-size: 18px），常用于响应式布局

#### vw-vh
* rem的弊端
* 网页视口尺寸
* vw/vh

### 四种定位区别
* static是默认
* relative 相对定位 相对于自身原位置进行偏移
* absolute 绝对定位 相对于最近已定位的祖先元素
* fixed 固定定位的元素会相对于视窗口对位

### 三栏布局，左右顶宽高，中间自适应
#### 1.float布局
**兼容性好，但需要清除浮动**
```bash
#css样式
*{
  padding: 0;
  margin: 0;
}
.wrapper{
  width: 100%;
  height: 300px;
}
.right{
  float: right;
  width: 300px;
  height: 300px;
  background-color: red;
}
.left{
  float: left;
  width: 300px;
  height: 300px;
  background-color: gray;
}
.center{
  background-color: green;
  height: 300px;
}
#html布局
<div class="wrapper">
  <div class="left"></div>
  <div class="right"></div>
  <div class="center">
    <h1>float布局</h1>
    1.三栏布局的中间
    2.三栏布局的中间
  </div>
</div>
```

#### 2.绝对定位布局
**快捷，但有效性不行**
```bash
# css样式
.wrapper{
  position: relative;
  width: 100%;
  height: 300px;
}
.right{
  position: absolute;
  right: 0;
  width: 300px;
  height: 300px;
  background-color: red;
}
.left{
  position: absolute;
  left: 0;
  width: 300px;
  height: 300px;
  background-color: gray;
}
.center{
  position: absolute;
  right: 300px;
  left: 300px;
  background-color: green;
  height: 300px;
}
#html:
<div class="wrapper">
  <div class="left"></div>
  <div class="center">
    <h1>float布局</h1>
    1.三栏布局的中间
    2.三栏布局的中间
  </div>
  <div class="right"></div>
</div>
```
**优点：**
绝对定位布局简单方便，不容易出问题；能够优先加载重要内容；网页缩放时能保持正常布局。
**缺点：**
只适用固定高度的布局。绝对定位元素脱离文档流，父元素会出现高度塌陷，只能通过给父元素设置固定高度才能解决这一问题。
#### 3.flex布局
**比较完美的**
```bash
.wrapper{
  display: flex;
}
.right{
  flex: 0 0 300px;
  height: 300px;
  background-color: blue;
}
.left{
  flex: 0 0 300px;
  height: 300px;
  background-color: red;
}
.center{
  flex: 1;
  height: 300px;
  background-color: yellow;
}
```
**优点：**
绝对定位和相对定位都会引起元素脱离文档流，flex布局能够避免这个问题。flex布局实现简单，可以实现各种布局。flex是一个比较完美的方案。目前移动端的布局也都是用flexbox。 flex布局是未来的趋势。
**缺点：**
不能兼容IE9及以下浏览器。
#### 4.表格布局
**兼容性高，ie8兼容性也可以**
```bash
.wrapper{
  width: 100%;
  height: 300px;
  display: table;
}
.wrapper div{
  display: table-cell;
}
.left{
  width: 300px;
  background: red;
}
.right{
  width: 300px;
  background: blue;
}
.center{
  background: yellow;
}
```
**优点：**
实现容易，兼容性好；网页缩放时能保持正常布局
**缺点：**
一栏超出高度，其他栏也跟着变高，可能并不是我们期待的效果；无法设置栏间距；不能优先加载重要内容

#### 5.网格布局
**新知识点符合珊栏布局的思想**
```bash
.wrapper{
  display: grid;
  width: 100%;
  grid-template-rows: 100px;
  grid-template-columns: 300px auto 300px;
  }
.left{
  background: red;
}
.right{
  background: blue;
}
.center{
  background: yellow;
}
```
**优点：**
绝对定位和相对定位都会引起元素脱离文档流，flex布局能够避免这个问题。grid布局实现简单，可以实现各种布局。目前虽然只支持高版本的浏览器，相信不久的将来会流行起来。
**缺点：**
A.一栏超出高度，其他栏也跟着变高。可能并不是我们期待的效果。
B.只支持高版本的浏览器，IE不支持（IE11支持，但需提供前缀），其他大部分需要较新版本的浏览器才支持。