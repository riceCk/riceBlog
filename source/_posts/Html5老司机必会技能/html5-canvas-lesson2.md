---
title: html5-canvas-lesson2
categories: Html5老司机必会技能
tags: HTML5
date: 2019-04-19 10:45:03
---
# 先初始化画布和画笔（执行上下文）
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d')
```

# 绘制线段
## 画线
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d')
ctx.moveTo(100, 100);  # 起始坐标
ctx.lineTo(200, 100);  # 画
ctx.lineTo(200, 200);  # 坐标
ctx.lineWidth = 10     # 设置画笔粗细
ctx.strokeStyle = 'red' # 改变画笔颜色
ctx.stroke();          # 开始画线段
ctx.beginPath();       # 另起路径
```
## 闭合路径,填充
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d')
ctx.moveTo(100, 100);  # 起始坐标
ctx.lineTo(200, 100);  # 画
ctx.lineTo(200, 200);  # 坐标
ctx.closePath()  #闭合路径
ctx.fill()       # 填充
ctx.stroke();
```
# 绘制矩形
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d')
ctx.rect(100, 100, 100, 100) #(起始x坐标，起始y坐标, 宽， 高)
ctx.stroke() # 描边
ctx.fill()  # 填充
```
## fillRect 自动填充
```bash
ctx.fillRect(x, y, dx, dy);
```
## strokeRect 自动描边
```bash
ctx.strokeRect(x, y, dx, dy)
```
## clearRect() 橡皮擦
```bash
clearRect(x, y, dx, dy) # (起始x位置， 起始y位置， 高度， 宽度)
```
## 实例矩形落地的动画效果
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d')
ctx.fillRect(100, 100, 50, 50)
let y = 100;
let timer = setInterval(() => {
  ctx.clearRect(0, 0, 300, 300);
  ctx.fillRect(100, y, 50, 50)
  y += 5
  if (y >= 250) {
    clearInterval(timer);
  }
}, 100)
```

# 绘制弧形
## ctx.arc(x, y, r, 起始弧度, 结束弧度, 弧度方向)
**弧度方向：**`0`表示顺时针，`1`表示逆时针
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d')
ctx.arc(100, 100, 50, 0, Math.PI * 2, 0);
ctx.stroke();
```

## arcTo(x1, y1, x2, y2, r) 绘制圆角
画一个100px的正方形，弧度border-radius: 10px;
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d')
ctx.moveTo(100, 110); # 起始点
ctx.arcTo(100, 200, 200, 200, 10);  
# 起始点连接(x1, y1), (x1, y1)连接(x2, y2), 做r的圆的切线弧
ctx.arcTo(200, 200, 200, 100, 10);
ctx.arcTo(200, 100, 100, 100, 10);
ctx.arcTo(100, 100, 100, 200, 10);
ctx.stroke();
```
# 贝塞尔曲线
## 二次贝塞尔曲线
quadraticCurveTo(x, y, ex, ey);
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d')
ctx.moveTo(100, 100);
ctx.quadraticCurveTo(200, 300, 400, 200);
ctx.stroke();
```
## 三次贝塞尔曲线
```
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d')
ctx.moveTo(100, 100);
ctx.bezierCurveTo(200, 300, 400, 200, 450, 250);
ctx.stroke();
```

# 坐标轴转换
## translate(dx, dy) 重新映射画布上的(0, 0)位置
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d');
ctx.translate(100, 100); # 将画布平移xy轴各100px
ctx.fillRect(-100, -100, 100, 100);
ctx.translate(-100, -100);
ctx.fillStyle = 'red';
ctx.fillRect(0, 0, 50, 50);
```
## scale(sx, sy)缩放当前绘图
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d');
ctx.translate(100, 100); # 将画布平移xy轴各100px
ctx.scale(2, .5);  # x轴放大2倍，y轴缩小.5
ctx.fillRect(0, 0, 100, 100);
```
## rotate(Math.PI) 旋转当前绘图
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d');
ctx.translate(100, 100); # 将画布平移xy轴各100px
ctx.rotate(Math.PI / 4) # 沿着(0, 0)画布当前坐标轴旋转
ctx.fillRect(0, 0, 100, 100);
```
## save() restore()
保存当前图像状态的一份拷贝（保存画布状态），从栈中弹出存储的图形状态并恢复
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d');
ctx.save() # 保存这个时刻的坐标轴状态，在一个栈中[tranlate(0,0)]
ctx.translate(100, 100); # 将画布平移xy轴各100px
ctx.save()  
# 保存平移之后的一个坐标轴的状态[tranlate(0,0), translate(100, 100)]
ctx.rotate(Math.PI / 4) # 沿着(0, 0)画布当前坐标轴旋转
ctx.fillRect(0, 0, 100, 100);

ctx.restore();  
# 取离restore最近的save时刻的状态[tranlate(0,0), translate(100, 100)]
ctx.fillStyle = 'red';
ctx.fillRect(0, 0, 50, 50);

ctx.restore();  
# 现在取的就是第一次坐标轴的状态[tranlate(0,0)]
ctx.fillStyle = 'green';
ctx.fillRect(0, 0, 50, 50);
```
## setTransform(a, b, c, d, e, f)先重置再变换
**参数：**水平缩放、水平倾斜、垂直倾斜、垂直缩放、水平移动、垂直移动
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d');
ctx.setTransform(1, Math.PI / 2, 0, .5, 100, 100);
ctx.fillRect(0, 0, 100, 100);
```
## transform(a, b, d, e, f) 在之前的基础上变换

# 渐变
## createLinearGradient(x1, y1, x2, y2)
线性渐变，必须在填充渐变的区域里定义渐变，否则无效果
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d');
let bg = ctx.createLinearGradient(0, 0, 500, 500)
bg.addColorStop(0, 'red');
bg.addColorStop(.5, 'green');
bg.addColorStop(1, 'yellow');
ctx.fillStyle = bg;
ctx.fillRect(0, 0, 500, 500);
```
## createRadialGradient(x1, y1, r1, x2, y2, r2) 径向渐变
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d');
let bg = ctx.createRadialGradient(250, 250, 250, 250, 250, 500)
bg.addColorStop(0, 'red');
bg.addColorStop(.5, 'green');
bg.addColorStop(1, 'yellow');
ctx.fillStyle = bg;
ctx.fillRect(0, 0, 500, 500);
```
## 例题
### 径向
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d');
let bg = ctx.createRadialGradient(200, 200, 100, 200, 200, 200);
bg.addColorStop(0, '#fff');
bg.addColorStop(.5, '#000');
bg.addColorStop(1, '#fff');
ctx.fillStyle = bg;
ctx.fillRect(0, 0, 500, 500);
```
# 阴影
这里的阴影偏移量不受坐标系变换的影响
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d');
ctx.shadowColor = 'red' # 阴影颜色
ctx.shadowBlur = 10;  # 模糊程度
ctx.shadowOffsetX = 10; # 阴影x轴偏移量
ctx.shadowOffsetY = 10; # 阴影y轴偏移量
ctx.fillRect(0, 0, 100, 100);
```
# 文本+阴影
```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d');
ctx.shadowColor = 'red' # 阴影颜色
ctx.shadowBlur = 10;  # 模糊程度
ctx.shadowOffsetX = 10; # 阴影x轴偏移量
ctx.shadowOffsetY = 10; # 阴影y轴偏移量
ctx.font = '20px sans-serif'
ctx.fillText('hello world', 100, 100) # 填充
ctx.strokeText('hello world', 100, 100) # 描边
```
# 线段样式
* lineCap = 'butt' 初始没有帽子
* lineCap = 'square' 方形帽子
* lineCap = 'round'  圆形帽子
* lineJoin = 'miter' 尖的
* lineJoin = 'bevel' 截断
* lineJOin = 'round' 椭圆的

## ctx.miterLimit
当lineJoin是miter时，用户控制斜接部分的长度如果斜接长度超过miterLimit的值，变成bevel

```bash
let oCanvas = document.getElementById('cans');
let ctx = oCanvas.getContext('2d');
ctx.moveTo(100, 100);
ctx.lineTo(100, 200);
ctx.lineWidth = 10;
// ctx.lineCap = 'square';
ctx.lineCap = 'round';
ctx.stroke();
```