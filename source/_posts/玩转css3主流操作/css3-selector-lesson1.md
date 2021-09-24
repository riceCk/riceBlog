---
title: css3-selector-lesson1
categories: 玩转css3主流操作
tags: css3
date: 2019-02-17 10:38:20
---
# 各大浏览器兼容标志

|prefix|browser|
|:----:|:----:|
|-webkit|chrome和safari|
|-moz|firefox|
|-ms|IE|
|-o|opera|

# 选择器---selectior
## 选择器类别
* 关系型选择器 Relationship Selectors
* 属性选择器  Attribute Selectors
* 伪元素选择器 Pseudo-Element Selectiors
* 伪类选择    Pseudo-Classes Selectiors

### 关系型选择器
* E + F 下一个/满足条件的兄弟/元素节点

```bash
<div>div</div>
<p>1</p>
<p>2</p>
<p>3</p>
-----------------------------------
div + p {}  # 选择的第一个<p>1</p>
```
* E ~ F 满足条件的所有兄弟元素节点

```bash
<div>div</div>
<p>1</p>
<p>2</p>
<p>3</p>
-----------------------------------
div ~ p {} # 选中的所有p标签
```

* E > F 直接子元素选择器

### 属性选择器
* E[F~="a"] 所有属性F的值为a的元素

```bash
<div data="a">1</div>
<div data="b">2</div>
<div data="a b">3</div>
<div data="b a c">4</div>
<div data="aa b c">5</div>
-----------------------------
div[data~="a"] {  # 满足条件的 1 3 4
  background-color: red;
}
```
* E[F|="a"] 以a开头，或者a-开头的元素

```bash
<div class="a">1</div>
<div class="b">2</div>
<div class="a-shbh b">3</div>
<div class="b bcda c">4</div>
<div class="aa b c">5</div>
-----------------------------------
div[class|="a"] { # 满足条件的  1 3
  background-color: red;
}
```
* E[att^="val"] 以`val`开头的元素
* E[att$="val"] 以`val`结尾的元素
* E[att*="val"] 只要存在`val`的元素都可以

### 伪元素选择器
* E::placeholder  只能改变input提示的color颜色

```bash
<input type="text" placeholder="请输入用户名">
-------------------------------------------------
input::placeholder {
  color: green;
}
```
* E::selection 改变鼠标选中区域的样式

```bash
<div>阿凯很帅</div>
<div>帅比凯特别帅</div>
---------------------------------
 div:nth-of-type(1)::selection {
  color: #fff;
  background-color: #fcc;
  text-shadow: 3px 5px #000;  # 选中后文字阴影
}
div:nth-of-type(2)::selection {
  color: yellow;
  background-color: green;
}
```

### 伪类选择器,被选择元素的一种状态
* E:not[F='A'] 除了F类型为A的名称

```bash   
div:not([class='text']){  #除了类名text的
  background-color: red;
}
div:not([class]){        # 除了有class类的
  background-color: red;
}
li:not(:last-of-type) { # 除了最后一个
  border-bottom: 1px solid black;
}
---------------------------------
<div class='dom'>阿凯很帅</div>
<div class='dom'>帅比凯特别帅</div>
<div class='text'>123</div>
<div>帅比凯特别帅</div>
```
* :root 在html中根节点与html选择是一致的

* E:target 被loaction.hash的js属性选中的元素，或者a标签

```bash
div:target {
  border: 1px solid red;
}
-----------------------------
<a href="#box1">box1</a>
<a href="#box2">box2</a>
<div id="box1">box11</div>
<div id="box2">box22</div>
```

* E:first-child 被选择E的第一个标签
* E:last-child 被选择E的最后一个标签

```bash
p:first-child {
  background-color: red;
}
------------------------------
<p>4</p>   # 可以选中
<div>
  <p>1</p> # 可以选中
  <p>2</p>
  <p>3</p>
</div>
```
* E:only-child  父级下面E标签唯一的标签才选中
* E:nth-child(n) n从0开始计数，css从1开始选择，n=event偶数，n=odd基数
* E:nth-last-child(n) 是`nth-child(n)`的倒过来计数
* `E:first-child`、`E:last-child`、`E:only-child`、`E:nth-child(n)`、`E:nth-last-child(n)`这五种元素都要考虑其他元素的干扰

```bash
p:nth-child(2n+1) {
  background-color: red;
}
---------------------------
<div>
  <p>1</p> # 选中
  <p>2</p>
  <p>3</p> # 选中
  <p>4</p>
  <p>5</p> # 选中
  <p>6</p>
  <p>7</p> # 选中
</div>
<p>2</p>
<p>3</p>  # 选中
<p>4</p>  
<p>5</p>  # 选中
```
* E:first-of-type 第一个p元素，不考虑其他元素
* E:last-of-type 最后一个p元素不考虑其他元素

```bash
p:first-of-type{
  background-color: red;
}
-------------------------
<div>
  <span>0</span>
  <p>1</p>   # 选中
  <p>2</p>
</div>
<p>2</p>    # 选中
<p>3</p>
```

* E:only-of-type 父级唯一类型的E标签才选中

```bash
p:only-of-type{
  background-color: red;
}
----------------------------
<div>
  <span>0</span>
  <p>1</p>  # 选中
</div>
```
* E:nth-of-type(n) 不管其他类型，只以E类型为准,odd基数，event偶数
* E:nth-last-of-typa(n) 是`nth-of-type(n)`的倒过来计数

* E:empty 这个元素里啥都没有

```bash
div:empty {
  border: 1px solid black;
  height: 100px;
}
-----------------------------------
<div><span>123</span></div>
<div></div> # 选中
<div>345</div>
```

* E:enabled 能被选中的
* E:disabled 不能被选中的
* E:read-only 可读不可选

```bash
input:enabled {
      background-color: green;
}
input:disabled {
  border: 1px solid #f20;
  background-color: #fcc;
  box-shadow: 1px 2px 3px #f20;
}
input:read-only {
  color: red;
}
-----------------------------------
<input type="text">
<input type="text" disabled>
<input type="text" readonly value='只能瞅着，啥也干不了'>
```