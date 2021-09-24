---
title: 走进jQuery世界
categories: jQuery
tags: jquery
date: 2018-02-04 18:22:00
---
# 选择器
## 层级
* selector1 selector2 后代选择器
* parent > child 子级选择器
* prve + next  紧挨着后一个元素
* prve ~ sliblings 选择peve后面同级的标签

## 基本筛选器
* :first 第一个
* :last  最后一个
* :not(selector) 非
* :even   基数（根据索引1开头）
* :odd    偶数（根据索引1开头）
* :eq(index)  指定第几个

## 内容
* :contains(text)  选择内容文本
* :empty           不包含子元素或文本的
* :has(selector)   包含selector元素
* :parent          返回有子元素的

## 属性
* [attribute]       指定attribute属性
* [attribute=value] 指定attribute=value元素
* [attribute!=value] 指定attribute！=value属性
* [attribute^=value] 指定attribute以value开头

## 子元素
:first-child    第一个子元素
:first-of-type  第一个子元素类型
:last-child     
:last-of-type
:nth-child

# jQuery对象访问 
* each(callback)
* length
* selector
* data(removeData)
* index([selector|element])
* get([index])

## each 遍历元素
```bash
$('#list li').each(function(){
  console.log(this)
  $(this).css('color', 'red')
})
```
## data
```bash
$('.wrapper').data('age', '19') #存属性
$('.wrapper').data('age')      #读属性
$('.wrapper').removeData('age')  #删除
```
## index 取索引
```bash
$('li').index()
```
## get  输出索引取元素
```bash
$('li').get(2)
```
# jquery 应用
## 属性
### attr
```bash
#读class值
$('div').attr('class') 
 #写入class值
$('div').attr('class', 'box2 box')
#写入多个属性
$('div').attr({'data': 'num', 'age': 19}) 
# 遍历满足条件的所有属性，必须return出一个数
$('li').attr('class', function(index, val){
  console.log(index, val);
  return 'list'
})
```
### removeAttr
```bash
#移除div的data属性
$('div').removeAttr('data')
#移除wrapper值。就class
$('.wrapper').removeAttr('class')
```
### prop、removeProp
```bash
<input type="text" checked="true">
$('input').prop('checked')  #=> true
$('input').attr('checked')  #=> checked
$('input').prop('checked', false)  
```
## CSS类
### addClass、removeClass、toggleClass
```bash
$('ul').addClass('list') #ul添加list样式
$('li').removeClass('item')  #删除li中的item类
$('li').toggleClass('first') #first切换
```
## html类
### html、val、text
```bash
$('.wrapper').html('<p>内容</p>') #包含标签，文本
$('.wrapper').text('内容') # 只包含文本
$('input').val()         # 表单的内容
``` 
## css类
```bash
$('.wrp').css({color: 'red'})
$('.wrp').css('color','red')
# 视觉上的距离
$('.wrp').offser().left/top  #jq
$('.wrp').offsetWidth/offsetHeight # js（包含padding及margin）
# 作用于有定位的父级
$('.wrp').left/top          #jq
$('.wrp').offsetLeft/offsetRight #js
#css设置的height值
$('.wrp').height()  
#height + padding
$('.wrp').innerHeight() 
#height + padding + border
$('.wrp').outerHeight()
#height + padding + border + margin 
$('.wrp').outerHeight(true) 
```
## 文档处理
### 内部插入
在选中元素内部插入，父子级
* append、appendTo
* prepend、prependTo 插入元素的前面

```bash
#链式调用操作的是第一个所选元素
$('.wrp').append('<div class="box">111</div>')
$('<div class="box">111</div>').appendTo($('.wrp'))
```

### 外部插入
* after、before 前、后
* insertAfter、insertBefore 

在选中原色外部插入，兄弟级
```bash
$('div').after('<li></li>') #div前面插入li标签
$('input').insertBefore('.wrp') #input插到wrp前面
```

### 包裹
* wrap 给选中元素填一个父级
* unwrap 删除选中元素的父级

```bash
# 给box添加一个wrapper父级
$('.box').wrap('<div class="wrapper"></div>')
# 删除box的父级
$('.box').unwrap('<div class="wrapper"></div>')
```
### 替换
* replaceWith
* replaceAll

```bash
# 将input替换div
$('input').replaceWith('<div></div>')
# 将ul剪切到input位置
$('input').replaceWith($('ul'))
```

### 删除
* remove 
* detach

```bash
# 删除的同时删除其绑定事件
$('li').remove()
# 删除不会删除绑定事件
$('li').detach();
```

### 克隆 clone
```bash
$('ul').clone().appendTo($('body'))
```

## 筛选
### 过滤
* eq
* hasClass
* filter 筛选
* is

```bash
$('li').eq(1)   #选中li第一个
#输出true或false，只能判断class
$('li').hasClass('first') 
# 比hasClass更强大，属性都可以判断
$('li').is('#wrapper')
```
### 查找
* children只选择子元素，不会选出后代
* cloest自上查找，子级向上级，只找一个就返回
* find查找
* next在同辈中*向后*查找第一个满足的
* nextAll在同辈中*向后*查找所有满足的
* prev在同辈中*向上*查找第一个满足的
* prevAll在同辈中*向上*查找所有满足的
* siblings在同辈中查找*所有*满足的
* parent只找当前的*父级*
* parents查找当前所有上级（父级祖父）

### 串联
* add
* end

```bash
# 串联li和input一起设置
$('li').add('input').css('color', 'red');
# 返回的a标签，回退一格的意思
$('li a').parent().end()
```

## 工具类方法
* merage合并数组
* parseHTML 创建数组标签
* extend、fn.extend 克隆

### merage合并数组
```bash
var arr1 = [1, 2, 3];
var arr2 = [4, 5, 5];
$.merage(arr1, arr2);
# arr1 => [1, 2, 3, 4, 5, 5]
# arr2 => [4, 5, 5]
```

### parseHTML 创建数组标签
```bash
# 默认false，但true是允许创建script脚本标签
var html = $.parseHTML('<div></div><script>', true)
$('body').append(html);
```

### extend
```bash
var obj1 = {'a': 'aa', 'b':'bb'}
var obj2 = {'a': 'cc', 'd':'bb'}
$.extend(true, {}, obj1, obj2)
# {a: 'cc', b:'bb', d:'bb'}
# true表示深度克隆 
```