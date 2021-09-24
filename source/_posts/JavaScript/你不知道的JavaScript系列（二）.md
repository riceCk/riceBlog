---
title: 你不知道到JavaScript系列二     
categories: javascript
tags: 你不知道到JavaScript
date: 2018-11-3 10:25:14 
---

# 函数组合应用
## 对一串函数进行递归运算
### 开胃菜（reduceRight）
```bash
let arr = [65, 44, 33]
function getSum (total, num){
    console.log(total, num)
    # [33, 44, 77, 65, 142]
    return total + num
 }
function myFunction() {
    let aa = arr.reduceRight(getSum ,1)
    console.log(aa)
}
 myFunction()
```
```bash
function toUpperCase(str) {
    return str.toUpperCase() # ITEM
}
function add(str) {
    return str + '!' # ITRM!
}
function split(str) {
    return str.split('')
}
function reverse(arr) {
    return arr.reverse()
}
function join(arr) {
    return arr.join('-')
}
# while循环来使用的
function compose () {
    let args = Array.prototype.slice.call(arguments)
    let len =args.length - 1
    return function (x) {
        let result = args[len](x)
        while (len--) {
            result = args[len](result)
        }
        return result
    }
}
# 通过reduceRight
function compose1 () {
    let args = Array.prototype.slice.call(arguments)
    return function (x) {
        return args.reduceRight(function (res, cb) {
            return cb(res)
        },x)
    }
}
# es6 + reduceRigth
const compose2 = (...args) => x => args.reduceRight((res, cb) => cb(res), x)
let f = new compose1(add, join, reverse, split, toUpperCase)
f('item')
```

# 函数式编程-科里化
在数学和计算机科学中，科里化是一种将使用多个参数的一个函数转换成一个系列使用一个参数的函数的技术
## 前端why？科里化
前端使用科里化的用途主要就是简化代码结构，提高系统的维护性，一个方法，只有一个参数，强制了功能的单一性，很自然就做到了功能内聚，降低耦合
科里化优点就是降低代码重复，提高代码的适应性
### 简单的科里化小菜
```bash
function add(a, b, c, d) {
    return a + b + c +d;
}
function FixedParamsCurry(fn){
    let _arg = Array.prototype.slice.call(arguments, 1)
    // _arg = [1, 2]
    return function () {
        // arguments [2, 3]
        let newArg = _arg.concat([].slice.call(arguments, 0))
        return fn.apply(this, newArg)
    }
}
let newAdd = FixedParamsCurry(add, 1, 2)
newAdd(2, 3)  // 8
```
但第一次定义函数时所传的参数，在第二次执行的时候必须全部补齐。不够味道，下面有更精彩
### 上面例题升级版
```bash
function add(a, b, c, d) {
    return a + b + c +d;
}
function FixedParamsCurry(fn){
    let _arg = Array.prototype.slice.call(arguments, 1)
    return function () {
        let newArg = _arg.concat([].slice.call(arguments, 0))
        return fn.apply(this, newArg)
    }
}
function Curry(fn, length) {
    var length = length || fn.length
    return function () {
        if (arguments.length < length) {
            // [fn].concat([1])[fn, 1]
            var combined = [fn].concat([].slice.call(arguments, 0))
            return Curry(FixedParamsCurry.apply(this, combined), length - arguments.length)
        } else {
          return fn.apply(this, arguments)
        }
    }
}
var newAdd = Curry(add)
console.log(newAdd(1)(2)(3, 4))
```
无论第一次传几个参数，之后调用函数只要能调满即可。
### 实战：数据多次数据请求
**背景：** 但一个页面产生多次数据请求的时候容易出现代码的重复；比如：
ajax('post', 'www.test1.com', 'name=cck&code=111')
ajax('post', 'www.test1.com', 'key=111')
ajax('post', 'www.test2.com', 'name=cck&code=111)'
ajax('post', 'www.test2.com', 'key=222')
同一*POST*请求其实可以不用每次都传，相同的api接口不同的参数传递，也可以不用每次都传接口
```bash
function Curry(fn, length) {
    var length = length || fn.length
    return function () {
        if (arguments.length < length) {
            // [fn].concat([1])[fn, 1]
            var combined = [fn].concat([].slice.call(arguments, 0))
            return Curry(FixedParamsCurry.apply(this, combined), length - arguments.length)
        } else {
          return fn.apply(this, arguments)
        }
    }
}
function ajax (method, url, data) {
    console.log(method, url, data)
}
var ajaxCurry = Curry(ajax);
var PostAjax = ajaxCurry('post')
PostAjax('www.test1.com', 'name=cck&code=111')
PostAjax('www.test2.com', 'name=cck&code=111')
```
# 数据处理-扁平化
对多位数组进行数据处理，变成一维数组
```bash
var arr = [
    1, 2, 3, [
        4, 5, 6, [
            3, [4], [55,5,5], {arr: [123, 234, 555, [3434, 12, 434, 34]], obj: null, }
            ], 2344, 344
    ],
    132,null,21354
]
var testArr = [1, 2, [3, 4]]

function isArray (obj) {
    return Object.prototype.toString.call(obj) === '[object Array]'
}
function isObject (obj) {
    return Object.prototype.toString.call(obj) === '[object Object]'
}
// 方案一
function flatten1(arr) {
    var arr = arr || [],
        resArr = [],
        len = arr.length
    for (var i = 0; i < len; i++) {
        if ( isArray(arr[i]) ) {
            return resArr.concat(flatten1(arr[i]))
        } else {
            resArr.push(arr[i])
        }
    }
    return resArr
}
flatten1(arr)
// 方案二
Array.prototype.flatten2 = function () {
    var resArr = []
    this.forEach((item) => {
        Object.prototype.toString.call(item) === '[object Array]' ? resArr = resArr.concat(item.flatten2()) : resArr.push(item)
    })

    return resArr
}
arr.flatten2()
// 方案三
function flatten3(arr) {
    var arr = arr || []
    return arr.reduce(function (prev, next){
        return Object.prototype.toString.call(next) === '[object Array]' ? prev.concat(flatten3(next)) : prev.concat(next)
    }, [])
}
// 方案四
const flatten4 = arr => arr.reduce((prev, next) => {
    Object.prototype.toString.call(next) === '[object Array]' ? prev.concat(flatten3(next)) : prev.concat(next)
}, [])
```

# 性能优化-惰性函数
背景：需要执行一个t值，当再次使用t值的时候不需要重新计算t，且不需要if判断
## 方案一
重复执行test函数得出的值，通过if判断组织再次进行计算得出的，不好
```bash
var t = null
function test () {
    if (t) {
        return t;
    }
    return t = new Date().getTime()
}
```
## 方案二改进
通过闭包+函数名重新赋值优美的实现的惰性
```bash
let test = function () {
    let t = new Date().getTime();
    test = function () {
        return t
    }
    return test();
}
```
## 实战
当需要封装一个工具库的时候，因为工具库中的函数肯定是频繁使用的，所以可以运动惰性函数执行。 
```bash
function addEvent (dom, type, handler) {
    if (dom.addEventListener) {
        dom.addEventListener(type, handler, false);
        addEvent = function(dom, type, handler) {
            dom.addEventListener(type, handler, false);
        }
    } else {
        dom.attachEvent('on', + type, handler)
        addEvent = function (dom, type, handler) {
            dom.attachEvent('on', + type, handler)
        }
    }
}
```

# 公式导出鼠标位置判断
假定定义一个`id为box的div`,**getDer**为计算函数
```bash
let oBox = document.getElementById('box')
// 判断鼠标在元素移入移出的方向 （上下左右）
// mouseenter -> 鼠标移入  mouseleave -> 鼠标移出
function bindEvent() {
    oBox.onmouseenter = function(e) {
        get(e, 'in')
    }
    oBox.onmouseleave = function (e) {
        get(e, 'out')
    }
}
function get (event, state) {
    let d = getDer(event)
    let dir = ''
    switch (d) {
        case 0:
            dir = '-top';
            break;
        case 1:
            dir = '-right';
            break;
        case 2:
            dir = '-bottom';
            break;
        case 3:
            dir = '-left';
            break;
    }
    console.log(state + dir)
}
function getDer (event) {
    let w = oBox.offsetWidth;
    let h = oBox.offsetHeight;
    let l = oBox.offsetLeft;
    let t = oBox.offsetTop;
    let x = (event.clientX - l - w / 2) * (w > h ? (h / w) : 1);
    let y = event.clientY - t - h / 2 * (h > w ? (w / h) : 1);
    let d = (Math.round((Math.atan2(y, x) * (180 / Math.PI) + 180) / 90 ) + 3) % 4
    // (-180 ~ 180) + 180 = (0 ~ 360) / 90 + 3  % 4
    // top: -135 ~ -24  + 180 = 45 ~ 135             /90 --> 1  + 3 = 4 % 4 = 0
    // right 0 ~ 45    + 180  = 135 ~ 225            /90 --> 2  + 3 = 5 % 4 = 1
    // bottom 45 ~ 135 + 180  = 225 ~ 315            /90 --> 3  + 3 = 6 % 4 = 2
    // left -180 ~ -135 + 180 = 0 ~ 45 or 315 ~ 360  /90 --> 0 or 4 + 3 = 3/7 % 4 = 3
    return d;
}
bindEvent()
```

# 淘宝放大镜
## css样式
```bash
*{
    margin: 0;
    padding: 0;
}
.wrapper {
    height: 500px;
    width: 500px;
    border: 1px solid black;
    position: relative;
    margin: 20px;
}
.imgCont {
    height: 100%;
    width: 100%
}
.img {
    height: 100%;
    width: 100%;
    background: url('./3.jpg') 0% 0% no-repeat;
    background-size: 500px 500px;
}
.moveBox {
    position: absolute;
    cursor: move;
    background-color: #eee;
    opacity: 0.3;
    z-index: 999;
}
.bigImg {
    position: absolute;
    top: 0;
    left: 105%;
    width: 500px;
    height: 500px;
    border: 1px solid black;
    display: none;
    overflow: hidden;
}
```
## html
```bash
<div class="wrapper">
    <div class="imgCont">
        <div class="img"></div>
        <div class="moveBox"></div>
    </div>
    <div class="bigImg">
        <img src="./3.jpg" alt="">
    </div>
</div>
```
## javascript
```bash
let wrapper = $('.wrapper')
let imgCont = $('.imgCont')
let moveView = $('.moveBox')
let bigImg = $('.bigImg')
let w, h;
function bindEvent () {
    imgCont.on('mousemove', (e) => {
        move(e, 2)
    }).on('mouseleave', () => {
        moveView.hide()
        bigImg.hide()
    })
}
function move(event, mul) {
    w = 500 / mul;
    h = 500 / mul;
    let maxL = 500 - w
    let maxT = 500 - h
    let minL = 0
    let minT = 0
    let X = event.clientX - wrapper.offset().left - w / 2
    let Y = event.clientY - wrapper.offset().top - h / 2
    let endL = (X > minL) ? ((X < maxL) ? X : maxL) : minL
    let endT = (Y > minT) ? ((Y < maxT) ? Y : maxT) : minT
    moveView.css({
        'height': h + 'px',
        'width' : w + 'px',
        'display': 'block',
        'left': endL + 'px',
        'top': endT + 'px'
    })
    bigImg.css({
        'display': 'block'
    }).find('img').css({
        'width': mul * 500 + 'px',
        'height': mul * 500 + 'px',
        'margin-left': -endL * mul,
        'margin-top': -endT * mul
    })
}
bindEvent()
```

# 联动导航栏样式
## html布局
```bash
<div class="wrapper">
    <a href="javaSript:void(0)">
        <img src="./0.png" alt="">
    </a>
    <a href="javaSript:void(0)">
        <img src="./1.png" alt="">
    </a>
    <a href="javaSript:void(0)">
        <img src="./2.png" alt="">
    </a>
    <a href="javaSript:void(0)">
        <img src="./3.png" alt="">
    </a>
    <a href="javaSript:void(0)">
        <img src="./4.png" alt="">
    </a>
    <a href="javaSript:void(0)">
        <img src="./5.png" alt="">
    </a>
</div>
```
## css样式
```bash
* {
    margin: 0;
}
a {
    text-decoration: none;
}
.wrapper {
    position: absolute;
    bottom: 50px;
    left: 100px;
    width: 800px;
    text-align: center;
}
img {
    width: 64px;
    height: 64px;
}
```
## js
```bash
$('.wrapper').on('mousemove', function(e) {
    let event = e || window.event;
    function getDis (item) {
        var item = $(item)
        let x = parseInt(item.offset().left) + 64 / 2;
        let y = parseInt(item.offset().top) + 64 / 2;
        let a = Math.abs(event.clientX - x)
        let b = Math.abs(event.clientY - y)
        return Math.sqrt(Math.pow(a, 2) + Math.pow(b, 2))
    }
    for (let i = 0; i < $('img').length; i++){
        let d = getDis($('img')[i])
        d = Math.min(d, 200)
        let num = (200 - d) / 200 * 64 + 64
        $($('img')[i]).css({
            'width': num + 'px',
            'height': num + 'px'
        })
    }

})
```

# 随机数落地花
```bash
<div class="wrapper"></div>
setInterval(() => {
    let oDiv = $('<div class="redBox"></div>')
    let num = parseInt(Math.random() * 40) - 20
    oDiv.css({
        'top': -parseInt(Math.random() * 80),
        'left': parseInt(Math.random() * 240),
        'z-index': 10,
        'transform': `rotate(${num}deg)`
    }).appendTo($('.wrapper'))
    down(oDiv)
}, Math.random() * 200 + 400)
function down(div) {
    div.animate({top: '450px'}, 4000, function (){
        if (parseInt(div.css('top')) >= 400) {
            $(this).remove()
        }
    })
}
function getMath(min, max) {
    return Math.random() * (max - min) + min;
}
```