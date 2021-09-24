---
title: css3-animation-lesson6
categories: 玩转css3主流操作
tags: css3
date: 2019-03-21 17:28:03
---
# animation
## 先举个栗子
```bash
# style
div {
  position: absolute;
  width: 100px;
  height: 100px;
  background-color: red;
  animation: run 4s cubic-bezier(.5, 0, .5, 1) infinite; # infinite无限运动
}
@keyframes run {
  0% {
      top: 0;
      left: 0;
  }
  20%{
      left: 100px;
      top: 0;
  }
  50%{
      left: 100px;
      top: 100px;
  }
  75%{
      left: 0;
      top: 100px;
  }
  100%{
      top: 0;
      left: 0;
  }
}
# html
<div></div>
```
## animation-direction主要用来设置动画播放方向
```bash
# 无限反方向运动
animation: run 4s cubic-bezier(.5, 0, .5, 1) infinite reverse;
# 动画在奇数次（1、3、5...）正向播放，在偶数次（2、4、6...）反向播放
animation: run 4s cubic-bezier(.5, 0, .5, 1) infinite alternate;
# 动画在奇数次（1、3、5...）反向播放，在偶数次（2、4、6...）正向播放
animation: run 4s cubic-bezier(.5, 0, .5, 1) infinite alternate-reverse;
```
## animation-fill-mode 在动画开始之前和结束之后发生的操作
```bash 
# 动画在结束后继续应用最后的关键帧的位置
animation: run 4s cubic-bezier(.5, 0, .5, 1) forwards;
# 会在向元素应用动画样式时迅速应用动画的初始帧
animation: run 4s cubic-bezier(.5, 0, .5, 1) backwards;
# 初始第一针的状态，起始最后一针状态，也是常用的一种
animation: run 4s cubic-bezier(.5, 0, .5, 1) both;
```
## animation-timing-functiion 动画的过度类型
* linear: 线性(0, 0, 1, 1) 
* ease： 平滑(.25, .1, .25, 1) 
* ease-in 又慢到快(.42, 0, 1, 1) 
* ease-out: 又快到慢(0, 0, .58, 1)
* ease-in-out: 又慢到快再到慢(.42, 0, .58, 1) 
* step-start： 等同于steps(1, start)
* step-end: 等同于steps(1, end)
* steps([?], [start/end]) 接受两个参数的步进函数。第一个参数必须为正整数，指定函数的步数。第二个参数取值可以是start或end，指定每一步的值发生变化的时间点。第二个参数是可选的，默认值为end

```bash
animation: run 4s cubic-bezier(.5, 0, .5, 1) # 贝塞尔曲线函数
animation: run 4s linear # 线性过度(0, 0, 1, 1)
```
### animation-timing-function: steps(?, start/end)
大白话将就是自定义一个动画到另一个动画过滤的帧数，但帧数达到人眼不可察觉时就是所谓的无缝隙动画了，反之，就会有停顿感
```bash
# 第二个参数默认值end，保留当前帧状态，直到这段动画时间结束
# 比如0%到25%状态时，过渡保留0%状态直到时间跳转到25%；
# 所以当100%的状态时，瞬间到达感受不到其100%的状态变化
animation: change-color 4s steps(20, end);
# start 保留下一帧状态，直到这段动画时间结束
# 与end相反
animation: change-color 4s steps(20, start);
```
