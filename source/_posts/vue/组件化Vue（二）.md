---
title: 组件化Vue(二)
categories: Vue
tags: vue
date: 2018-07-18 09:55:00
---
# Vue简单语法糖
## 玩转class类名
### 建立html标签
```bash
<div id="app">
        <div class="demo" 
            @click="attach=!attach" 
            :class="{'red': attach, blue: !attach}"
        ></div>
        <div class="demo"
        @click="attach=!attach" 
        :class="divClass"
        ></div>
        <div class="demo" :class="color"></div>
        <input type="text" v-model="color">
        <div class="demo" :class="[color, {blue: !attach}]"></div>
    </div>
```
这里面有两个**class**，`:class`是动态的class，Vue会将动态的class处理合并在一起。Vue实现的就是dom中可以运行js代码，可能现在还不太习惯，想开一点就好了。
在`第一个demo`中`:class`绑定一个对象通过点击事件绑定**attach**是T或F来处理class类名。在`第二个demo`中将第一个对象提炼到js中的**computed**下。效果一样。
在`第三个demo和input`绑定在一起改变**class**值改变样式。`在第四个中`动态的绑定一个数组，初始值为**color**，通过**attach**来覆盖之前的样式。
### 建立js脚本语言
```bash
new Vue({
    el: "#app",
    data: {
        attach: false,
        color: "green"
    },
    computed: {
        divClass : function(){
            return {
                red: this.attach,
                blue: this.attach
            }
        }
    }
})
```
脚本语言很简单，该说了上面也说了。就此...
## 玩转style样式
万变不离其宗。还是一样的效果
### html标签
```bash
<div id="app">
    <div class="demo" :style = "{backgroundColor: color}"></div>
    <input type="text" v-model="color">
    <div class="demo" :style="divStyle"></div>
    <input type="text" v-model="width">
    <div class="demo" :style= "divStyle, {height: height + 'px'}"></div>
    <input type="text" v-model="height">
</div>
```
### js脚本代码
```bash
new Vue({
    el: "#app",
    data: {
        color: 'red',
        width: 100,
        height: 200
    },
    computed: {
        divStyle: function(){
            return{
                backgroundColor: this.color,
                width: this.width + 'px'
            }
        }
    }
    
})
```
自行参考一样的意思上例题一，绑定的class或者style中可以创建对象就写属性，可以创建数组，多个属性处理一起使用，也可以创建字符串，当然这其实是函数执行类型，js脚本return出结果
  
## v-if 和 v-else 应用
### html标签
```bash
<div id="app">
    <p v-if="show=='a'">你能看见我吗？我是a</p>
    <p v-else-if="show=='b'">你看得见我把 我是b</p>
    <p v-else>你看不见我，嘻嘻我是"其他"</p>
    <template v-if="show">
        <p>我是第一段p</p>
        <p>我是第二段p</p>
    </template>
    <button @click="show='b'">switch</button>
    <button @click="show='c'">switch</button>
</div>
```
`v-else`会自动找到离他最近的`v-else`进行逻辑解析,`template`标签不占dom节点H5的知识点
### js脚本代码
``` bash
new Vue({
    el: "#app",
    data: {
        show: 'a'
    }
})
```
## v-if 和 v-show 的区别
### 上html标签
```bash
<div id="app">
    <p v-if="show">你能看见我吗？</p>
    <p v-else>你看不见我，嘻嘻</p>
    <p v-show="show" >你看不见我。嘻嘻</p>
    <template v-if="show">
        <p>请输入用户名</p>
        <input type="text" placeholder="请输入用户名" key="username" >
    </template>
    <template v-else>
        <p>请输入密码</p>
        <input type="text" placeholder="请输入密码" key="password">
    </template>
    <button @click="show=!show">show</button>
</div>
```
`v-if`是false之后dom节点直接消失的，而`v-show`是通过**display=none**本质上的区别。
例上的两个input本质上一样的情况下vue为了优化性能就采用复用，所以要区别他们加一个`key`属性。

## v-for 应用
### html代码
```bash
<div id="app">
    <ul>
        <li v-for="(prop,index) in block">{{prop}}({{index}})</li>
    </ul>
    <template v-for="(prop,index) in block">
        <p>{{prop}}</p>
        <p>{{index}}</p>
    </template>
    <ul>
        <li v-for="person in persons">
            <p v-for="(value, key,i) in person">{{value}} | {{key}}</p>
        </li>
    </ul>
</div>
```
第一个ul是获取的数组，遍历缩影**prop**是值，**index**是索引很简单就不细说，第二个ul获取的是数组中嵌套对象形式，基本上开发都是这样，所以需要嵌套`v-for`,第一遍嵌套出来的是数组中每一位对象，里面的再次遍历**value**内部的值，获取属性名就是**key**,获取当前索引**i**。
### js脚本代码
```bash
new Vue({
    el: '#app',
    data: {
        block : ['11111', '22222', '33333'],
        persons: [{
            name: 'dengge',
            color: 'green'
        },{
            name: "chengge",
            color: 'gold'
        }]
    }
})
```
## v-for应用之添加、修改删除
### html 标签
```bash
<div id="app">
    <ul>
        <li v-for="(prop,index) in block" @click="change(index)">{{prop}}({{index}})</li>
    </ul>
    <button @click="newAdd">new add</button>
    <div v-for="value in inputValue" :key="value">
        {{value}} : <input type="text" >
    </div>
    <button @click="reverseInput">reverse</button>
</div>
```
进过for遍历渲染之后如果再次就该里面的值，值会改变，但是页面不会再重新渲染所以页面不变。那怎么办？？？看js脚本
当v-for便利input时候。vue为了提高性能会复用input标签。所以在标签上加上绑定`:key`标签
### js脚本
```bash
new Vue({
    el: '#app',
    data: {
        block : ['11111', '22222', '33333'],
        persons: [{
            name: 'dengge',
            color: 'green'
        },{
            name: "chengge",
            color: 'gold'
        }],
        inputValue: [1,2,3]
    },
    methods: {
        change: function(index){
            var arr = [].concat(this.block);
            arr[index] = '5555',
            this.block = arr;
        },
        newAdd: function(){
            this.block.push('7777');
        },
         reverseInput: function(){
            this.inputValue.reverse() //revers()数组方法倒叙
        }
    }
})
```
通过创建新的**arr**重新赋值带**block**中重新渲染页面，但当用push方法直接添加就可以了，因为这不是这不是之前的原始js引擎的push方法，而是Vue重新写的，把之前的覆盖掉了。

## Vue实例应用
### html标签
```bash
<div id="app">
    <h1>{{title}}</h1>
    <button @click="show" ref="myButton">让你能看见我</button>
    <p v-if="showP">你能看见我吗？</p>
    <hello></hello>
     <hello></hello> 
</div>
<div id="app2">
    <h1>{{title}}</h1>
    <button @click="changevm1">change Vm1</button>
    <hello></hello> 
</div>
```
`ref`属性，添加这个属性之后，在js代码中可以直接用**vm.$refs.myButton**，将其选出，js代码中的vm就是一个js对象，上面会有很多属性。通过`vm.`的形式可以将他们都选择操作。
### js脚本语言
```bash
Vue.component("hello", {
    template: '<h1>hello</h1>'
})
var vm = new Vue({
    el: "#app",
    data: {
        title: "thie VueJs instance",
        showP : false
    },
    methods: {
        show: function(){
            this.title = "the VueJs instance(update)"
            this.showP = true
        }
    },
    computed: {
        upperCase: function(){
            return this.title.toUpperCase(); //toUpperCase转义大写字符
        }
    },
    watch: {
        title: function(){
            alert(this.upperCase);
        }
    }
})
console.log(vm.$refs.myButton);
// setTimeout(function(){
//     vm.title = "change by ti",
//     vm.show()
// }, 3000)
var vm2 = new Vue({
    
    data:{
        title: "the second Vuejs"
    },
    methods: {
        changevm1: function(){
            vm.title = "change by vm2"
        }
    }
})
vm2.$mount("#app2");

var vm3 = new Vue({
    template: `<h1>hello</h1>`
})
vm3.$mount("#app3");
```
`vm2.$mount("#app2");`就是当**el**没有定义的时候，后者插入的,`template`模板可以查到**#app3**div中。与`$mount`连用。这里可用于组建复用上。
`hello`自定义的一个标签组建创建全局组建。把他插到Vue示例中就可以实现复用组件化
