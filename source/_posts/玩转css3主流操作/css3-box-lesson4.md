---
title: css3-box-lesson4
categories: 玩转css3主流操作
tags: css3
date: 2019-03-01 13:34:03
---

## box-sizing 混杂模式盒模型
```bash
box-sizing: content-box  # W3c标准
box-sizing: border-box   # ie6混杂模式
```
## overflow
* hidden 溢出部分隐藏
* visible 溢出部分不做处理
* scroll 溢出部分设置滚动条
* auto 溢出部分设置滚动条，不溢出不设置

## resize 用户页面拖动改变内容大小
```bash
width: 300px;
height: 500px;
border: 1px solid red;
resize: both;
overflow: hidden ;    
```

# dispaly 弹性盒子
## flex/inline-flex
```bash
display: flex 外敛弹性盒子
display: inline-flex 内敛弹性盒子
```

## dispaly:flex 弹性盒子属性
### 盒子属性
#### 切换默认轴方向
```bash
flex-direction: row; #默认方向，主轴->x轴，左->右
flex-direction: row-reverse; # 主轴 -> x轴， 右->左
flex-direction: column; # 主轴 -> y轴， 上->下
flex-direction: column-reverse; # 主轴 -> y轴，下->上
```

#### 换行
```bash
flex-wrap: wrap;  # 默认值，多出不换行，自动压缩
flex-wrap: nowrap; # 多出换行
flex-wrap: wrap-reverse; # 倒着换行，倒着有点匪夷所思
```
#### justify-content 主轴排列形式
```bash
justify-content: flex-start; # 默认主轴方向排列（向左）
justify-content: flex-end; # 相反方向排列
justify-content: center;  # 基于主轴中间
justify-content: space-between; # 基于两侧，中间盒子自适应对齐
justify-content: space-around; # 平分内部盒子两边间距相同
```

#### align-items 交叉抽（侧轴）排列形式(侧轴一条时)
```bash
align-items: flex-start; # 默认值，交叉着排列方向
align-items: flex-end;  # 交叉轴相反方向
align-items: center;    # 交叉着中间
align-items: baseline;  # 根据内部盒子中，文字位置进行对齐
align-items: staretch;  # 默认内部盒子不设置高，继承父级的高
```

#### align-content 侧轴多轴情况下排列
```bash
align-content: center; # 允许换行时，基于交叉轴，多轴居中
align-content: space-between; # 基于多行侧轴两侧，中间盒子自适应对齐
align-content: space-around;  # 基于多行侧轴两侧，分内部盒子两边间距相同
```

### 子级属性集合
#### order改变排序迅速
**order**默认值为0，设置之后为越大越靠前
```bash
.content:first-of-type {
  order: -2;
}
.content:nth-of-type(2) {
  order: -3;
}
```
#### align-self 设置个体在交叉轴上面的位置
* 父级`aling-item` VS `align-self`,取决于子级`aling-self`
* 父级`align-content` VS `align-self`, 取决于父级`aling-content`

```bash
align-self: flex-start;
align-self: flex-end;
align-self: center;  # 交叉轴中间
align-self: baseline; # 文字位置进行对齐
align-self: stretch;  # 默认内部盒子不设置高，继承父级的高
```

#### flex 子级属性
* flex-grow: 1; 当子级填充剩余空间比例

```bash
# 瓜分比例1：2：3
.content:first-of-type {
  flex-grow: 1;
}
.content:nth-of-type(2) {
  flex-grow: 2;
}
.content:nth-of-type(3) {
  flex-grow: 3;
}
```

* flex-basis 相当于覆盖width/height的属性值
只写basis，代表元素的最小宽度，设置width，并且basis小于width -》basis < realWidth(动态宽度) < width

* flex-shrink
加权值公式：200 x 1 + 200 x 1 + 400 x 3 = 1600；
**加权值公式：（真实内容区的大小） X shrink + ... X ** 
需要压缩的值：(200 + 200 + 400) - 600 = 200;
老大老二：width:200，flex-shrink:1; 所以压缩比例：(200px x 1) / 1600  = 0.125; 最后200 - 200(需要压缩的值) x 0.125 = 175px;
老三：width:400,flex-shrink:3; 所以压缩比例：(400px x 3) / 1600 = 0.75;
最后400 - 200(需要压缩的值) x 0.75 = 150px;

```bash
.content {
  width: 200px;
  height: 100px;
  flex-shrink: 1;
}
.content:nth-of-type(3) {
  width: 400px;
  flex-shrink: 3;
}
```

* flex-shrink压缩存在box-sizing属性时
压缩的值只算实际内容区域的值

```bash
.wrapper {
  width: 600px;
  height: 600px;
  border: 1px solid black;
  display: flex;
}

.content {
  width: 200px;
  height: 200px;
  flex-shrink: 1;
  box-sizing: border-box;
  padding: 0 80px;
  background-color: #f0f;
  /* border: 1px solid green; */
}

.content:nth-of-type(2) {
  background-color: #0ff;
}

.content:nth-of-type(3) {
  width: 400px;
  flex-shrink: 3;
  background-color: #ff0;
}
```

* flex: <flex-grow> <flex-shrink> <flex-basis>
flex: 1;计算为flex: 1 1 0%;
flex: auto;计算为flex: 1 1 auto;
flex: none; 计算flex: 0 0 auto;
