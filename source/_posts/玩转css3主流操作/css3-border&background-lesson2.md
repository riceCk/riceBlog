---
title: css3-border&background-lesson2
categories: 玩转css3主流操作
tags: css3
date: 2019-02-23 11:10:03
---

# border的拆
## 一级拆分
```bash
border-radius: 10px 20px 30px 40px;
```
## 二级拆分
```bash
border-top-left-radius: 10px;
border-top-right-radius: 20px;
border-bottom-right-radius: 30px;
border-bottom-left-radius: 40px;
```
## 三级拆分
```bash
border-top-left-radius: 10px 10px;
border-top-right-radius: 20px 20px;
border-bottom-right-radius: 30px 30px;
border-bottom-left-radius: 40px 40px;
```
## border-image
```bash
border-image: source slice repeat;
```
* border-image-source  可以填充图片，也支持渐变色

```bash
border-image-source: url(source/red.png); # 图片
border-image-source: linear-gradient(red, yellow);
```
* border-image-slice 图片切割属性

```bash
# 上右下左的顺序
border-image-slice: 10 10 10 10
```
![borderImageSlice](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/borderImageSlice.png)
* border-image-width 背景图片占据borde-width宽度的值

* border-image-outset 背景图片向外延伸的像素，填负值无用


* border-image-repeat
stretch 默认值 拉伸
round  拉伸加平铺，保证整个（比较理性）
repeat 多出复制平铺
space  多出的拿空白填充，但能铺满正比例多个在多一个

```bash
# 支持两个参数，水平 垂直
border-image-repeat: round space;
```



# box-shadow
## 水平偏移、垂直偏移、模糊度、伸缩、颜色
```bash
box-shadow: (inset) 0px 0px 0px 0px #off
box-shadow: 0px 0px 10px red, inset 0px 0px 10px red;
```
## 炫彩的圆，背景黑色
```bash
div {
  position: absolute;
  left: calc(50% - 150px);
  top: calc(50% - 150px);
  width: 300px;
  height: 300px;
  border-radius: 50%;
  box-shadow: inset 0px 0px 50px #fff,
              inset 10px 0px 80px #f0f,
              inset -10px 0px 80px #0ff,
              inset 10px 0px 300px #f0f,
              inset -10px 0px 300px #0ff,
              0px 0px 50px #fff,
              -10px 0 80px #f0f,
              10px 0 80px #0ff;
}
```
## 小太阳模糊效果
```bash
div {
  position: absolute;
  left: calc(50% - 25px);
  top: calc(50% - 25px);
  width: 50px;
  height: 50px;
  background-color: #fff;
  border-radius: 50%; 
  box-shadow: 0px 0px 100px 50px #fff,
              0px 0px 250px 125px #f20;
}
```

# background
## background-image
### 支持的参数
```bash
background-image: url()  #图片路径
background-image: linear-gradient(red, green); 线性渐变
background-image: radial-gradient() 镜像渐变
```
* background-image: linear-gradient(red, green);
to right、to top right等方向随意搭配

```bash
# 方向属性
background-image: linear-gradient(to right, red, green);
background-image: linear-gradient(0deg, red, green);
# 颜色起止位置
background-image: linear-gradient(90deg, red 20px, green);
```

* background-image: radial-gradient() 镜像渐变

```bash
# circle 圆,可调整圆心
background-image: radial-gradient(circle at right bottom, red, green) 
background-image: radial-gradient(circle at 0 100px, red, green) 
# ellipse 椭圆
background-image: radial-gradient(ellipse at right bottom, red, green)
# closest-corner 最近的角落
# closest-side   最近的边
# farthest-corner 最远的角落
# farthest-side  最远的边
```
### url()支持放多个
```bash
width: 200px;
height: 200px;
background-image: url('./source/pic1.jpeg'), url('./source/pic2.jpeg');
background-size: 100px 200px, 100px 200px;
background-position: 0 0, 100px 0;
background-repeat: no-repeat;
```

## background-origin从那边开始，默认从border结束
* border-box 从border开始 - border结束
* padding-box 从padding开始 - border结束
* content-box 从content开始 - border结束

## background-clip 从E开始剪切，是整体剪切，从开始到结束
* border-box
* padding-box
* content-box
* text  填充文字背景图片需要配合其他属性

```bash
background-image: url('./source/pic1.jpeg');
-webkit-background-clip: text;
background-clip: text;
-webkit-text-fill-color: transparent;
text-fill-color: transparent;
```

## background-position 定位根据background-origin的类型
```bash
background-image: url('./source/pic1.jpeg');
background-origin: border-box;
background-repeat: no-repeat;
background-position: 20px 20px;  # 根据border定位
```

## background-repeat 平铺属性
* no-repeat 不平铺
* round 拉伸 + 平铺，到达一定到达多出，只width方向有用
* space 间距 + 平铺，不会改变图片大小，多了空白平铺

```bash
background-repeat round space # 水平 垂直
```

## background-attachment
当`background-position`设置定位时，改变定位类型
* scroll 默认值，相对于容器定位，类似fixed
* local 相对于内容区定位，类型absoule
* fixed 相对于屏幕视口定位

```bash
# div中文字溢出的情况有滚动条
div {
  width: 500px;
  height: 500px;
  border: 1px solid red;
  overflow: auto;
  background-image: url('./source/pic1.jpeg');
  background-size: 300px;
  background-repeat: no-repeat;
  background-position: 100px 100px;
  background-attachment: fixed;
}
```

## background-size
* 像素

```bash
background-size: 300px 300px;
```
* cover 一张图片完完整整的填充完一个容器，不改变图片比例
* contain 将背景图像等比缩放到宽度或高度与容器的宽度或高度相等，背景图像始终被包含在容器内。
