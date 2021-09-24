---
title: css3-text-lesson3
categories: 玩转css3主流操作
tags: css3
date: 2019-02-28 16:28:03
---
# text 文本
## text-shadow 文字阴影
```bash
# 水平偏移量，垂直偏移量，模糊，颜色
text-shadow: 10px 10px 3px #000;
# 支持多个文字阴影
text-shadow: 10px 10px 3px black, -10px -10px 10px red;
``` 
### 浮雕效果
```bash
text-shadow: 1px 1px #000, -1px -1px #fff;
```
### 镂刻效果
```bash
text-shadow: -1px -1px #000, 1px 1px #fff;
```

### 鼠标触发火焰效果
```bash
:root{ 
  background-color: #000;
}
div {
  position: absolute;
  left: calc(50% - 350px);
  top: 100px;
  width: 700px;
  height: 100px;
  font-size: 80px;
  color: #ddd;
  text-shadow: 0px 0px 10px #0f0,
                0px 0px 20px #0f0,
                0px 0px 30px #0f0;
  transition: all 2s;
}
div:hover {
  text-shadow: 0px 0px 10px #f00,
                0px 0px 20px #f10,
                0px 0px 30px #f20; 
}
div::before {
  content: 'NO ';
  opacity: 0;
  text-shadow: 0px 0px 10px #f00,
                0px 0px 20px #f10,
                0px 0px 30px #f20,
                0px -5px 20px #f10,
                0px -10px 20px #f20,
                0px -15px 30px #f10,
                0px -20px 30px #f20,
                0px -30px 40px #f10,
                0px -40px 50px #f20; 
  transition: all 2s;
}
div:hover::before {
  opacity: 1;
}
```

### 眼花迷乱的效果
```bash
div {
  position: absolute;
  left: calc(50% - 350px);
  top: 100px;
  width: 500px;
  height: 150px;
  font-size: 60px;
  font-weight: bold;
  color: #f10;
  text-shadow: 0 0 5px #f10,
                0 0 10px #f20,
                0 0 15px #f30;
  transition: all 1.5s;
}
div:hover {
  text-shadow: 10px -10px 10px #f00,
                10px 10px 10px #ff0,
                -10px 10px 10px #f0f,
                -10px -10px 10px #00f,
                15px -15px 15px #00f,
                20px 15px 15px #0f0,
                -15px 30px 15px #0ff,
                -15px -20px 15px #ff9;
} 
```

## text-stroke 文字描边效果
```bash
-webkit-text-stroke: 1px red;
```

## word-break
* keep-all 文本不允许字符类换行
* break-all 强制超出换行
* break-word 超出换行，但是尽可能的保持一个英文单词的完整性
 
## columns 多列属性
### column-count 自适应几列
```bash
column-count: 3;
```
### column-gap 每列之间空隙
```bash
column-gap: 30px;
```

### column-rule每列之间的border，与border属性值一致
```bash
column-rule: 1px solid black;
```

### column-span 让文字中的某个标签悬浮出来
```bash
column-span: all
```

### column-width 每列的宽度
根据父级的宽度时刻改变，会有一定的拉伸
```bash
column-width: 300px
```