---
title: 组件化Vue(一)
categories: Vue
tags: vue
date: 2018-07-18 09:47:00
---
# Vue简单语法糖
## Vue引用
### 首先先引入Vue
``` bash
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
```

### 然后建立html标签
``` bash
<div id="app">
    <input type="0" v-on:input="changeTitle"> #`事件绑定`
    <p v-once>{{title}}</p>   #只绑定一次=>hello world 
    <p>{{sayHello()}} <a v-bind:href="link">baidu</a> </p>     
    # hello
    <p v-html="linkBaidu"> </p>
</div>  
```
v-bind 绑定数值
### js语法
``` bash
new Vue({
    el : "#app",  #选择器
    data : {      #数据绑定
        title: "hello world!!!!",
        link: "http://www.baidu.com",
        linkBaidu: "<a href = 'httm://www.baidu.con'>baidu</a>"
    },
    methods : {   #函数保管
        changeTitle: function(event){  #事件
            this.title = event.target.value;
        },
        sayHello: function(){   #方法
            this.title = "hello"
            return this.title
        }
    }
})
```
Vue支持数据绑定，通过 **new** 建立Vue对象，**el** 选择，**data** 数据绑定，**methods** 函数集合多方面配合。标签中还设有多种组件，**v-on:input="xxx"** 是input事件绑定，小程序也采用这种方式。
**v-once**  只绑定一次数据，即为初始值，不根据后面的变化而变化， 双大括号只渲染成字符串， **v-html** 将后面的参数渲染成dom节点，可以不是使用这种组件尽量不使用，容易遭到脚本跨域攻击

##  关于事件问题
### 关于html标签的设置
``` bash
 <div id="app">
    <button v-on:click = "increase(2, $event)">click me!!!</button>
        //手动传参之后用$event传入代表事件源对象
    <p>
        {{ count }}
    </p>
    <p v-on:mousemove = "setXY">x/y: {{x}} / {{y}} 
        <span v-on:mousemove.stop.prevent = "" >stop</span>
                    //.stop事件修饰符取消默认事件(如冒泡) 
    </p>
                
    <input type="text" v-on:keyup.enter = "aliert" >
        //.enter 事件回车修饰符 。space空格事件。 es键盘去事件。 
</div>
```
### 关于js脚本的设置
``` bash
new Vue({
    el: "#app",
    data: {
        count : 0,
        x : 0,
        y : 0
    },
    methods: {
        increase: function(step, event){
            this.count += step
        },
        setXY: function(event){
            this.x = event.clientX,
            this.y = event.clientY
        },
        aliert: function(){
            alert(111)
        }
    }
})
```
标签中支持 **v-on:** 加各类事件，函数可添加参数，添加参数之后，需要用上事件源event的时候，必须传 **$event** ,还有事件修饰符可支持链式调用

## v-on和v-bind与v-model的关系
### html标签
``` bash
<div id="app">
    <input type="text" v-bind:value = "name" v-on:input = "changeName" > 
    <p> {{name}} </p>

    <input type="text" v-model = "model" >
    <p> {{model}} </p>
</div>
```
### js脚本
``` bash
new Vue({
    el: "#app",
    data: {
        name: "change",
        model: "model"
    },
    methods: {
        changeName: function(e){
            this.name = e.target.value;
        }
    }
})
```
两种方式的数据双向绑定，**v-on** 监听浏览器改变js脚本，**v-bind** js脚本改变浏览器，这两种形成数据绑定。Vue之后提供了一个便捷的数据绑定  **v-model**

## Vue computed和methods以及watch
### html标签
``` bash
<div id="app">
    <button @click="increase++">increase</button>
    <button v-on:click="increase--">decrease</button>
    <button v-on:click="increaseSecond++">increaseSecond</button>
    <p> {{increase}} </p>
    <p> {{increaseSecond}} </p>
    <p> {{ result()}} | {{resultCom }} </p>
            <!-- result() 是一个函数执行的形式 -->
</div>
```
其中result() 是一个函数执行的形式，Vue对于事件的** v-on: ** 缩写形式`@`，**v-bin：**缩写成`:`只需要一个冒号就行了。并且其中的**increase++**、**increase--**是直接改变data中的值。
### js脚本代码
``` bash
new Vue({
    el: "#app",
    data: {
        increase: 0,
        increaseSecond: 0
    },
    computed: {  //计算属性
    
        resultCom: function(){
            return  this.increase > 5 ? "greater than 5" : "less than 5"                    
        }
    },
    
    methods: {
        result : function(){
            return  this.increase > 5 ? "greater than 5" : "less than 5"            
        }
    },
    watch: {
        increase: function(){
            var vm = this;
            setTimeout(function(){
                vm.increase = 0
            }, 2000)
        }
    }
})
```
`computed`和`methods`应用一样都是执行dom中的函数，但是computed他可以检测到内部变化此例中**increase**的属性。只有当**increase**的属性变化才会执行computed,但是methods不一样他不分析其中的逻辑代码，只要上面dom变化他就运行,这是一个性能问题，之后的运用之还主要以**computed**为主
`watch`是一个监听属性，不会创建新的属性，只用于监听已有属性，此例题中是用来监听**increase** 这个变量，当**increase**这个变量改变了，就会执行下面的函数，这样可以异步操作了
## v-click 事件
### 事件修饰符
```bash
# 阻止单击事件继续传播 
<a v-on:click.stop="doThis"></a>

# 提交事件不再重载页面 
<form v-on:submit.prevent="onSubmit"></form>

# 修饰符可以串联 
<a v-on:click.stop.prevent="doThat"></a>

# 只有修饰符 
<form v-on:submit.prevent></form>

# 添加事件监听器时使用事件捕获模式 
# 即元素自身触发的事件先在此处处理，然后才交由内部元素进行处理 
<div v-on:click.capture="doThis">...</div>

# 只当在 event.target 是当前元素自身时触发处理函数 
# 即事件不是从内部元素触发的 
<div v-on:click.self="doThat">...</div>
```
使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 v-on:click.prevent.self 会阻止所有的点击，而 v-on:click.self.prevent 只会阻止对元素自身的点击。
# 更多vue属性查询请点击 [vue文档](https://cn.vuejs.org/v2/guide/events.html)