---
title: css3-transition-lesson5
categories: 玩转css3主流操作
tags: css3
date: 2019-03-01 13:34:03
---

# transition: all 2s linear 0s;
## transition-property 监听某个属性的改变
```bash
transition-property: all; # 监听所有属性的改变
transition-property: width, height;
 #监听width和height属性的改变 
```
## transition-duration 时间间隔（s）
```bash
transition-duration: 1s;
```

## transition-timing-function 运动变速状态
*linear： 线性过渡。等同于贝塞尔曲线(0.0, 0.0, 1.0, 1.0) ease： 
*平滑过渡。等同于贝塞尔曲线(0.25, 0.1, 0.25, 1.0)
*ease-in： 由慢到快。等同于贝塞尔曲线(0.42, 0, 1.0, 1.0) 
*ease-out： 由快到慢。等同于贝塞尔曲线(0, 0, 0.58, 1.0) 
*ease-in-out： 由慢到快再到慢。等同于贝塞尔曲线(0.42, 0, 0.58, 1.0) 
*step-start： 等同于 steps(1, start) step-end： 

```bash
transition-timing-function: linear # 匀速
```
### cubic-bezier属性函数
B(t) = P₀(1 - t)³ + 3P₁(1 - t)² + 3P₂t²(1 - t) + p₃t³,t∈[0, 1]
```bash
transition-timing-function: cubic-bezier(0, 0, 1, 1)
# 这是三次贝塞尔曲线，起点是(0, 0),重点(1, 1)
```
## transition-delay 时间延迟（等多少秒运动）
```bash
transition-delay: 0; # 默认0秒
```