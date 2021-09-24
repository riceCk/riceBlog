---
title: 你不知道到JavaScript系列一     
categories: javascript
tags: 你不知道到JavaScript
date: 2018-07-23 15:51:14 
---

# 重源码! bind的模拟实现

```bash
# 实例一
<div>dsfd</div>
<srcipt>
var list = {
    init: function(){
        this.message = 'hello';
        this.bindEvent();
    },
    bindEvent: function(){
        #this = list
        var oDiv = document.getElementsByTagName('div')[0];
        oDiv.onclick = this.show.bind(this);
    },
    show: function(){
        # 通过bind方法讲this转向list实例，否则this指向div实例
        console.log(this) 
    }
}
list.init();
</srcipt>

#实例二
<srcipt>
var value = 0;
var obj = {
    value: 1
}
function show (name, age){
    console.log(this.value);
    console.log(name, age);
}
var newShow = show.bind(obj, 'cst', 18)
# => bind的第一个参数是this指向，之后的参数是show的实参
</srcipt>

# 在function原型上封装一个bin()
<srcipt>
Function.prototype.newBind = function(target){
    #这边应该参数只写target，只取第一个参数即是this指向的地址
    target = target || window;
	var self = this;
    # 这里的arguments是newBind()中的参数
	var args = [].slice.call(arguments, 1);
    # silce创建从原数组索引1开始的新数组
    var temp = function(){};
    var F = function(){
        # 这里的arguments是new之后新函数的参数
        var _arg = [].slice.call(arguments, 0)
        return self.apply(this instanceof temp ? this : target,args.concat(_arg))
        # concat方法是连接arg数组和_arg数组成新数组
    }
    temp.prototype = this.prototype;
    F.prototype = new temp();
    return F;
}
</srcipt>
```

# 函数式编程之纯函数
纯函数的定义是，对于相同的输入，永远会得到相同的输出，而且没有任何可观察的副作用，也不依赖外部环境的状态，及是不依赖，不修改其作用域之外的变量的函数
## 小试牛刀
```bash
let a = [1, 2, 3]
function foo(x) {
    x.push(4)
    x; # [1, 2, 3, 4]
    # 然后
    x = [4, 5, 6]
    x.push(7)
    x; # [4, 5, 6, 7]
}
foo(a)
a; # 是[1, 2, 3, 4], 不是[4, 5, 6, 7]
```
**疑惑？**
我们向函数传递a的时候，实际是将引用a的一个复本赋值给x，而a仍然指向`[1, 2, 3]`。在函数中我们可以通过引用x来更改数组的值（push（4）之后变成`[1, 2, 3, 4]`）。但`x = [4, 5, 6]`并不影响a的指向，所以a仍然指向`[1, 2, 3, 4]`
## 纯函数思路
```bash        
var arr = []
function add (_arr) {
    var obj = {name: 'cst'}
    _arr.push(obj)
}
add(arr)
# 不是纯函数
# arr会发生改变，这就是引用值和原始值的区别
var arr = [{name: 'duyi'}]
function add(_arr) {
    var obj = {name: 'cst'}
    var newArr = [];
    for (var i = 0; i < _arr.length; i++) {
        newArr[i] = _arr[i];
    }
    newArr.push(obj)
    return newArr
}
```
## 纯函数实例：bug守恒定律
在JavaScript中你可以很容易的创建全局变量，这些变量可以在所以函数中访问到。这也是一个导致bug的常见原因，因为程序中的任务部分都可能修改全局变量从而导致函数的行为出现异常函数的行为出现异常
```bash
var personArr = [{name: '王港', src: '3.png', des: '梵蒂冈数据库', sex:'m'}]
var oIp = document.getElementById('input')
oInp.oninput = function () {
    filterByText(this.value, personArr)
}
function filterByText (text, arr) {
    var newArr = [];
    for (var i = 0; i < arr.lenth; i++) {
        arr[i].name.indexOf(text) != -1 ? newArr.push(arr[i])
    }
}
```

# 函数角度性能优化函数记忆
在内存与队列中选择最优效率
## 玩转阶乘
```bash
let cache = []
let count = 0
function factorial(n) {
    count ++
    if(cache[n]) {
        return cache[n]
    } else {
        if (n == 0 || n == 1) {
            cache[0] = 1
            cache[1] = 1
            return 1
        } else {
            cache[n] = n * factorial(n-1)
            return cache[n]
        }
    } 
}
factorial(5)
# 1! = 1 => 1次
# 2! = 2 * 1! => 2次
# 3! = 3 * 2! => 2次
# ...
```
**count=9**进行优化之后的函数执行的次数少了很多，随着n的增大，会变得更明显

## 优化阶乘函数（升级版）
```bash
function factorial(n) {
    if (n == 0 || n == 1) {
        return 1
    } else {
        return n * factorial(n-1)
    }
}
function memorize(fn) {
    var cache = {}
    return function() {
        var key = arguments.length + Array.prototype.join.call(arguments)
        if(cache[key]) {
            return cache[key]
        } else {
            cache[key] = fn.apply(this, arguments)
            return cache[key]
        }
    }
}
var newF = memorize(factorial)
```

# 防抖&节流
## 节流
函数节流就是预定一个函数只要在不于等于执行周期时才执行，周期内调用不执行
* 窗口调整
* 页面滚动
* 抢购疯狂点击

### 针对疯狂点击问题
```bash
<div id="show">0</div>
<button id="btn">click</button>
<script>
    var oDiv = document.getElementById('show')
        var oBtn = document.getElementById('btn');
        function throttle (handler, wait) {
            var lastTime = 0;
            return function (e) {
                var nowTime = new Date().getTime()  # 1970年一月一日
                if (nowTime - lastTime > wait) {
                    handler.apply(this, arguments)
                    lastTime = nowTime
                }
            }
        }
        function buy (e) {
            oDiv.innerText = parseInt(oDiv.innerText) + 1;
        }
        oBtn.onclick = throttle(buy, 1000)
</script>
```

## 防抖
函数防抖就是在函数需要频繁触发情况时，只有足够空间的时间，才执行一次

```bash
<input type="text" id="inp"></input>
<script>
    var oInp = document.getElementById('inp')
    var timer = null;
    function ajax () {
        console.log(this.value)
    }
    oInp.oninput = function() {
        clearTimeout(timer)
        timer = setTimeout(function() {
            ajax.apply(_self, arguments)
        }, 1000)
    };
</srcipt>
```

# js数学之美
## 拖拽公式-物体拖拽事件
```bash
<div id="box"></div>
    <script>
        var oBox = document.getElementById('box');
        var body = document.getElementsByTagName('body')[0];
        function bindEvent (ele, wrap) {
            var X,
                Y,
                boxL,
                boxT,
                disL,
                disT;
            var drag = false;
            ele.onmousedown = function (e) {
                drag = true;
                var event = e || window.event;
                X = event.clientX;
                Y = event.clientY;
                boxL = ele.offsetLeft;
                boxT = ele.offsetTop;
                disL = X - boxL;
                disT = Y - boxT;
                console.log(disL, disT)
            }
            body.onmousemove = function (e){
                var event = e || window.event;
                if (drag) {
                    ele.style.left = event.clientX - disL + 'px';
                    ele.style.top = event.clientY - disT + 'px';
                }
            }
            ele.onmouseup = function () {
                drag = false;
            }
        }
        bindEvent(oBox, body);
    </scritp>
```

## canvas中的数学应用-扇形进度条
```bash
<canvas id="can" height='500px' width='500px'></canvas>
<div id="box"></div>
    <script>
        var can = document.getElementById('can')
        var oBox = document.getElementById('box')
        var ctx = can.getContext('2d')
        CanvasRenderingContext2D.prototype.sector = function (x, y, r, sDeg, eDeg) {
            # 圆心点，半径，角度 --> 起始角度，终止角度
            this.save();
            this.beginPath();
            this.moveTo(x, y);
            # 角度 -> 弧度
            this.arc(x, y, r, sDeg * Math.PI / 180, eDeg * Math.PI / 180, false);
            this.closePath()
            this.restore()
            return this
        }
        var timer;
        var angle = 0;
        var per;
        ctx.fillStyle = 'red'
        # angle / 360 = per / 100
        timer = setInterval(() => {
            angle += 5
            ctx.sector(250, 250, 100, 0, angle).fill();
            per = (5 * angle / 18).toFixed(2)
            oBox.innerHTML = '加载中：' + per + '%'
            if (angle === 360) {
                clearInterval(timer)
                oBox.innerHTML = '加载完成！'
            }
        }, 200)
    </script>
```

## 实战利器拖拽拓展-拖拽加覆盖

# UI多线程-深入剖析Js执行机制
## 流程器常驻的线程
* JS引擎线程（解释执行js代码、用户输入、网络请求）
* GUI线程（绘制用户界面、与js主线程是互斥的）
* http网络请求线程（处理用户的get、post等请求，等返回结果后将回调函数推入任务队列）
* 定时器触发器线程（setTimeout、setInterval等待时间结束后把执行函数推入任务队列中）
* 浏览器事件处理线程（将click、mouse等交互事件发生后将这些事件放入事件队列中）

## JS引擎线程和GUI线程-互斥
JS可以操作Dom元素，进行会影响到GUI的渲染结果，因此JS引擎线程与GUI渲染线程是互斥的，也就是说当js引擎线程处于运行状态时，GUI渲染线程处于冻结状态

## JS执行机制-如果是多线程的不好吗
js设计出来就是为了与用户交互，处理Dom，加入js是多线程，同一时间一个线程想要修改Dom，另一个线程想要删除Dom，问题就变的复杂许多，浏览器不知道听谁的，如果引入“锁”的机制，这不就又回到了被其他尴尬的困境了吗？

## Js执行机制
导图表达的内容用文字来表述的话：
同步和异步任务分别进入不同的执行“场所”，同步的进入主线程，异步的进入Event Table并注册函数。
当指定的事件完成时，Event Table会将这个函数移入Event Queue
主线程内的任务执行完毕为空，会去Event Queue读取对应的函数，进入主线程执行。
上述过程会不断重复，也就是常说的Event Loop（事件循环）

## 同步任务
```bash
function foo (ot) {
    function bar (it) {
        console.log(it)
    }
    bar(20)
    console.log(ot)
}
foo(10)
```
* 代码没有执行的时候，执行栈为空栈
* foo函数执行时，创建了一个帧中包含了形参、局部变量（预编译过程），然后把这一帧压入栈中
* 然后执行foo函数内代码，执行bar函数 
* 创建新栈，同样有形参、局部变量，压入栈中
* bar函数执行完毕，弹出栈
* foo函数执行完毕，弹出栈
* 执行栈为空

# 重源码！call,apply手写实现与应用
## 封装call源码
```bash
Function.prototype.newCall = function() {
    let ctx = arguments[0] || window
    let args = []
    ctx.fn = this
    for (let i = 1; i < arguments.length; i++) {
        args.push('arguments[' + i + ']')
    }
   
   #  eval -> 可以直接执行eval中的函数/运算
    let result = eval('ctx.fn(' + args.join(',') + ')')
    delete ctx.fn
    return result
}
```
## 使用
```bash
let value = 'window';
let obj = {
    value: 'obj',
};
function show (name, age) {
    console.log(this.value)
    return {
        name: name,
        age: age
    }
}
# obj.show = show()
show.call(obj)
let reslut = show.newCall(obj, 'deng', 30)
console.log(reslut)
```

## 封装apply
```bash
Function prototype.newApply = function(ctx, arr) {
    let ctx = ctx || window
    let result 
    ctx.fn = this
    if(!arr) {
        result = ctx.fn()
        delete ctx.fn
    } else {
        let args = []
        ctx.fn = this
        for (let i = 1; i < arr.length; i++) {
            args.push('arr[' + i + ']')
        }
        result = eval('ctx.fn(' + args.join(',') + ')')
        delete ctx.fn
    }
    return reslut
}
```

# 对抽象等于进行详解
## 1、字符串和数字之间的相等比较
* 如果Type(x)是数字，Type(y)是字符串，则返回x == ToNumber(y)的结果
* 如果Type(x)是字符串，Type(y)是数字，则返回ToNumber(x) == y的结果

## 2、其他类型和布尔值之间的比较
* 如果Type(x)是布尔类型，则返回ToNumber(x) == y的结果
* 如果Type(y)是布尔类型，则返回x == ToNumber(y)的结果

## 3、null和undefined之间的相等比较
* null == undefined

## 对象和非对象之间的相等比较
* 如果Type(x)是字符串或者数字，Type(y)是对象，则返回x == ToPrimitive(y)的结果；
* 反之一样 