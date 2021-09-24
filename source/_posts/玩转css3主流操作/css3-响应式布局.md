---
title: css3-响应式布局
categories: 玩转css3主流操作
tags: css3
date: 2019-04-18 21:37:03
---

# 响应式网页开发方法
## 流体网格
可伸缩的网格（大小宽高 都是可伸缩（可用flex或者百分比来控制大小）float） -> 布局上面元素大小不固定可伸缩
## 弹性图片
图片宽高不固定（可设置min-width: 100%）
## 媒体查询
让网页在不同的终端上面展示效果相同，（用户体验相同 -> 让用户用的更爽），不同的设备（大小不同分辨率不同） 上面均展示合适页面
## 主要断点
设备宽度的临界点

# 媒体查询引入
媒体查询会被权重影响
## 外部引入
屏幕小于375px时引入
```bash
<link rel='stylesheet' media='screen and (max-with:375px)' href='index.css'>
```
```bash
@import 'index.css' screen and (max-width:375px)
```

## css样式引入
```bash
@media screen and (max-with:375px) {
  div {
    width: 100%;
    height: 100%;
    background-color: red;
  }
}
```

# 媒体查询操作符
## 或关系（300 < x < 375px）
```bash
@media screen and (max-with:375px) and (min-width: 300px) {
  div {
    width: 100%;
    height: 100%;
    background-color: red;
  }
}
```
## 且（300 or 600）
```bash
@media screen and (max-width: 375px), (min-width: 600px) {
  div {
    width: 100%;
    height: 100%;
    background-color: red;
  }
}
```
## 否
```bash
@media not screen and (max-width: 375px) {
   div {
    width: 100%;
    height: 100%;
    background-color: red;
  }
}
```

# 单位值
## rem
相对于根标签,相对html的font-size大小
```bash
<div class="demo">123</div>
html {
  font-size: 14px;
}
.demo {
  width: 10rem;
}
# 此时的实际width为140px，相对html的font-size大小
```
## em
相对于本身的font-size大小
```bash
# 此时实际width为150px
.demo {
  font-size: 15px;
  width: 10rem;
}
```
## vw，vh
相对于视口的宽度、高度，视口被均分为100单位

## vmin, vmax
* vmax -> 取视口宽高最大的一边分为100份
* vmin -> 取视口宽高最小的一边分成100份
