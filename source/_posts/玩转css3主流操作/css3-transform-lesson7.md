---
title: css3-transform-lesson7
categories: 玩转css3主流操作
tags: css3
date: 2019-03-26 14:16:00
---
# 3D视角属性
```bash
perspective: 800px;  /* 景深 */
transform-style: preserve-3d; /* 添加3d效果 */
perspective-origin: 300px 300px; /* 添加角度 */
```
# transform: rotate()
```bash
transform: rotateX();
transform: rotateY();
transform: rotateZ();
transform: rotate3d(x, y, z, 30deg);
# 这里的参数x,y,z是一个矢量，遵循类似力的作用合力
transform: rotateX(45deg) rotateY(90deg)
# x,y,z是根据旋转顺序改变的，所以主要顺序
```
# transform: scale(x, y)
伸缩是次元素变化坐标轴的刻度
 
# transform: skew(x, y)
必须填角度，默认(0deg, 0deg)

# transform: transform: translate(x, y);

# perspective 景深
添加在父级上
```bash
perspective: 800px;
```
# 子级每个独立元素的景深
```bash
transform:perspective(800px)
```
# matrix
## 矩阵运算
```bash
|a,c,e|   |x|   |ax + cy + e|
|b,d,f| * |y| = |bx + by + f|
|0,0,1|   |1|   |0  + 0  + 1|
```
### matrix = transloate 平移
```bash
|1,0,e|   |x|   |x + e|
|0,1,f| * |y| = |x + f|
|0,0,1|   |1|   |1    |

matrix(1,0,0,1,e,f) === transloate(x, y)
```
### matrix = scale 伸缩
```bash
|a,0,0|   |x|   |ax|
|0,d,0| * |y| = |dy|
|0,0,1|   |1|   |1 |
matrix(a,0,0,d,0,0) === scale(x,y)
```

### matrix = rotate
```bash
|cos(θ),-sin(θ),e|   |x|   |cos(θ)x - sin(θ)y + e|
|sin(θ), cos(θ),f| * |y| = |sin(θ)x + cos(θ)y + f|
|0,      0,     1|   |1|   |1                    |
matrix(cos(θ), -sin(θ), sin(θ), cos(θ), 0, 0) = rotate(θ)
```
