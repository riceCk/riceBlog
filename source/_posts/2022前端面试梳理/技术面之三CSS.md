---
title: 技术面之二简历CSS
categories: 2022前端面试梳理
tags: 面试题
date: 2022-04-03 09:48:00
---

# HTML面试题
## 如何理解HTML语义化
* 让人更容易读懂（增加代码可读性）
* 让搜索引擎更容易读懂（CEO）

## 默认情况下，哪些HTML标签是块级元素，哪些是内联元素
* display:block/table;有div、h1、table、ul、p等（会独占一行）
* display：inline/inline-block；有span、img、input、button等（不会独占一行）

# Css面试题
## 分析知识模块
* 布局
* 定位
* 图文样式
* 响应式
* CSS3

## 布局（1）

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

## 定位
### absolute 和 relative 分别依据什么定位
* relative 依据自身定位
* absolute 依据最近一层定位元素

### 定位元素
* absoulte relative fixed
* body

### 水平居中
* inline元素：text-align:center
* block元素：margin: auto;
* absolute元素：left:50% + margin-left负值

### 垂直居中
* inline元素：line-height的值等于height值
* absolute元素：top：50% + margin-top负值
* absoulte元素：transform(-50%, -50%)
* absoulte元素：top,left,right,bottom = 0;margin: auto

## line-height如何继承
* 写具体数值，如30px，则继承该值
* 写比例，如2/1.5,则继承改比例
* 写百分比，如200%,则继承计算出来的值


## 响应式
### rem是个长度单位
* px，绝对长度单位，最常用
* em，相对长度单位，相对于父元素，不常用
* rem，相对长度单位，相对于根元素（如：font-size: 18px），常用于响应式布局

### vw-vh
* rem的弊端
* 网页视口尺寸
* vw/vh

# 四种定位区别
* static是默认
* relative 相对定位 相对于自身原位置进行偏移
* absolute 绝对定位 相对于最近已定位的祖先元素
* fixed 固定定位的元素会相对于视窗口对位