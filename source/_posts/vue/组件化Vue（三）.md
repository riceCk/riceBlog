---
title: 组件化Vue(三)
categories: Vue
tags: vue
date: 2018-07-18 10:47:00
---
# Vue简单语法糖
## Vue实例生命周期
### 周期图
![生命周期图示](https://raw.githubusercontent.com/riceCk/riceBlog/master/2018/07/02/post-img/lifecycle.png)

### 父级->子级->孙子
```bash
parent      beforeCreate
parent      data
parent      create
parent      beforeMount
son         beforeCreate
son         data
son         create
son         beforeMount
grandson    beforeCreate
grandson    data
grandson    create
grandson    beforeMount
grandson    mount
son         mount
parent      mount
parent      beforeUpdate
parent      update
---------- 孙子交互操作 --------
parent      beforeUpdate
son         beforeUpdate
grandson    beforeUpdate
grandson    update
son         update
parent      update
---------- 销毁时间 -------------
parent      beforeDestroy
son         beforeDestroy
grandson    beforeDestroy
son         destroyed
parent      destroyed
```
### html标签详解
```bash
<div id="app">
    <p>{{title}}</p>
    <button @click="title='updated'">change</button>
    <button @click="title='destory'">destory</button>
</div>
```
### js脚本函数的调用顺序
```bash
new Vue({
    el: "#app",
    data: {
        title: "the vue instance"
    },
    #数据方法的初始化
    beforeCreate: function(){
        console.log("beforeCreate1");
    },
    #数据绑定，渲染参数方法在里面
    created: function(){
        console.log("created2");
    },
    #el或template编译的方式，但是只挂在没定义
    beforeMount: function(){
        console.log("beforeMount3");
    },
    #编译好的模板重新覆盖渲染到html dome节点里去
    mounted: function(){
        console.log("mounted4");
    },
    #change点击之后，data中的值改变重新渲染触发
    beforeUpdata: function(){
        console.log("beforeUpdata5");
    },
    #change点击之后，data中的值改变重新渲染触发
    updated: function(){
        console.log("updated6");
    },
    #destory点击之后 执行的destroy()
    beforeDestroy: function(){
        console.log("beforeDestroy7");
    },
    #destory点击之后 执行的destroy()
    destroyed: function(){
        console.log("destroyed8");
    },
    methods: {
        destory: function(){
            this.$destroy();
        }
    }
})
```
`this.$destroy();`将Vue实例总dome节点中卸载去。不是删除节点，只是删除把数据绑定，事件等方式，只显示最终的结果

## Vue 组件化开发
### 全局组件和局部组件
```bash
<div id="app">
    <ck-cmp></ck-cmp>
    <ck-cmp2></ck-cmp2>
</div>
<div id="app2">
    <ck-cmp></ck-cmp>
</div>
```
组件化命名规则`'公司/个人'-'项目名称'`，通过这样防止污染其他组件。其中`<ck-cmp></ck-cmp>`是全局组件。可以定义在任意的Vue实例上。`<ck-cmp2></ck-cmp2>`是局部组件，只能定义在app实例上。下面看js源码
### js源码
```bash
#全局组件
Vue.component("ck-cmp", {
    #template 只有一个根节点
    template: `<div><div>123</div></div>`
})
new Vue({
    el: "#app",
    #局部组件
    components :{
        'ck-cmp2' :{
            template: `<div>
                    <div>hello world</div>
                </div>`
        }
    }
})
new Vue({
    el: "#app2"
})
```
全局组件创建就是在全局上定义，局部的组件就是在局部定义。

## 组件的两种数据绑定区别
### html代码
```bash
<div id="app">
    <ck-cmp></ck-cmp>
    <ck-cmp></ck-cmp>
</div>
```
### js源码
```bash
var data = {
    title: "hello"
}
#全局组件
Vue.component("ck-cmp", {
    #template 只有一个根节点
    template: `<div><div @click="title = 'hello world'">{{title}}</div></div>`,
    data : function(){
        return {
            title: "hello"
        }
        #return data
    }
})
```
这边可以有两种数据绑定，第一种全局`var`一个data对象然后在return中引用，那么数据绑定就是连带性复用。点击一个标签，弄一个也改变数据。第二种是在内部直接return一个对象,数据就是独立绑定的 
## 单项数据流 props属性
### html标签中可以传参数
```bash
<div id="app">
    <ck-cmp title='ckk' age='123' v-show="show"></ck-cmp>
    <button @click="show = !show">click</button>
</div>
```
`props`可以自定义属性，定义到html标签中传值
### js源码
```bash
Vue.component("ck-cmp", {
    template: `<div><div @click ="t ='helle world'">{{t}} {{age}}</div></div>`,
    data: function(){
        return {
            t: this.title
        }
    },
    props: ['title', 'age']
})
new Vue({
    el: "#app",
    data: {
        show : true
    }
})
```
`porps可以穿数组也可以穿对象，可以传过个值，通过**html标签**赋值，传到**props**里面，然后通过data数据绑定。
### props 对象类型可以限制属性
```bash
Vue.component("ck-cmp", {
    template: `<div><div @click ="t ='helle world'">{{t}} {{age}}</div></div>`,
    data: function(){
        return {
            t: this.title
        }
    },
    props: {
        title : {
            type: String,
            default: "hello"
            required: true
        },
        age: {
            type: Number,
            default: 20
        }
    }
})
new Vue({
    el: "#app",
    data: {
        show : true,
        age: 123
    }
})
```
`props`对象类型的，**type**限定数据类型，**default**设置默认属性，**required** 设置必须赋值。
