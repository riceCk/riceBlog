---
title: 玩转vue全家桶，从入门到源码（下）
categories: Vue
tags: vue
date: 2018-07-19 10:47:00
---
# 大型项目必用利器———vuex
## 先在vue项目中安装vuex
```bash
cnpm install vuex --save
```
* 创建store文件夹下index.js文件
* 配置index.js
* main.js引入vuex

## main.js的引入配置
```bash
  import Vue from 'vue'
  import App from './App'
+ import store from './store'

Vue.config.productionTip = false

var vm = new Vue({
  el: '#app',
+ store,
  components: { App },
  template: '<App/>'
})
console.log(vm);
```
# 为了方便理解下面的构建了实例图像
![实例结构图](https://github.com/riceCk/riceBlog/blob/master/images/vuex-2.png)
# vuex五中方法的关系图
![vuex五中方法的关系图](https://github.com/riceCk/riceBlog/blob/master/images/vuex-1.png)
## store下的index.js的相关配置
### store下的参数应用
* state 状态  数据
* mutations 定义修改数据方法（同步）
* actions 定义异步修改数据的方法 异步执行之后，触发mutations当中定义的函数同步改变state种的值
* getters 根据state当中的数据派生出新的值（与computed计算属性类型）

```bash
* modules {     模块
    a: {
        state,
        mutaions,
        getters,
        actions,
        modules
    },
    b: {}
}
```
#### 数据存放在state对象中
```bash
import vue from 'vue'
import vuex from 'vuex'
vue.use(vuex)
export default new vuex.Store({
    state:{
        val: 'hello'
        # 数据存储在vue实例上，$store.state.val上
    }
})
```
#### state在其他模块中可以有两种取值方式，推荐第二种
* 第一种最原始的翻家底

vue.$store.state中取值
```bash
$store.state.val
# 有定义就可得知数据存放地方。取值之时有情况而定前面是否加this或其他
```
* 第二中vuex.mapState函数调用取值，传所需要的参数，输出值

```bash
<script>
import { mapState } from 'vuex'
# mapSate == vuex.mapState
# mapState 是一个函数，放回所传入的参数对应在state中的值
export default {
    #computed: mapState(['val'])
    computed: {
        ...mapState({newVal: state => state.val})
    }
}
</script>
```
`mapState`函数支持两种传参，数组and对象，推荐第二种：
1、可以**模块化**；
2、可以重新名为**取值**名称，方式污染命名空间

函数在computed计算属性中执行，因为数据是可以一直变化的,`...`是对象展开运算符。
#### mutations 定义修改数据方法（同步） 两种方法
在index.js中，定义changeval函数，谁修改谁就调用。mutations只提供一个环境
```bash
<script>
export default new vuex.Store({
    state:{
        val: 'hello'
    },
    mutations: {
        changeVal(state, newVal){
            state.val = newVal
        }
    }
})
```
* 第一种运用 vue.$store.commit方法

以companieE.vue为例，通过`v-model`绑定input中的val参数修改`state`中的数据
```bash
<script>
export default {
    data(){
        return {
            val: this.$store.state.val
        }
    },
    watch: {
        val(){
            #用来触发mutations方法下的函数,第一个参数调用函数，第二个参数改变的值
            this.$store.commit('changeVal', this.val);
        }
    }
}
</script>
```
* 第二种 运用vuex.mapMatations方法

还是以companieE.vue为例，
```bash
<script>
import { mapMutations } from 'vuex'
# 提取vuex中的mapMutations方法
export default {
    data(){
        return {
            val: this.$store.state.val
        }
    },
    methods:{
        #触发changeVal函数，在需要的地方引用changeVal函数
        #...mapMutations(['changeVal'])
        ...mapMutations({setVal: 'changeVal'})
    },
    watch: {
        val(){
            this.setVal(this.val);
        }
    }
}
</script>
```
mapMutations支持传两种参数，数组and对象，对推荐第二种
* 方便模块化，当父级和自级都有changeVal的时候，通过子级重命名调用，不会污染父级，反之也是如此。
* 防止命名空间污染

#### actions 定义异步修改数据的方法 
#### 异步执行之后，触发mutations当中定义的函数同步改变state种的值
**前言：** 当使用mutations方法的时候，如果定义的函数是异步操作的（如：定时器），那么当调用函数改变数据是，只改变了`mutations.payload`临时数据，但是`state.val`的数据并没有改变，导致提交的数据和当前的数据不一致，，怎么办？ `actions`解决
index.js中操作，定义`asyncChangeVal`方法，异步执行`mutations`,保障了提交的数据和当前的数据一致
```bash
export default new vuex.Store({
    state:{
        val: 'hello'
    },
    mutations: {
        changeVal(state, newVal){
            # setTimeout(() => {
                state.val = newVal
            # }, 1000)            
        }
    },
    actions: {
        # ctx相当于vue.$store镜像
        asyncChangeVal(ctx, newVal){
            setTimeout(() => {
                ctx.commit('changeVal', newVal)
            }, 1000)
        }
    }
})
```
**当然调用asyncChangeVal也有两种方法**
* 第一种 运用vue.$store.dispatch方法

```bash
<script>
import { mapMutations } from 'vuex'
export default {
    data(){
        return {
            val: this.$store.state.val
        }
    },
    watch: {
        val(){
            this.$store.dispatch('asyncChangeVal', this.val);
        }
    }
}
</script>
```
* 第二种 运用vuex.mapActions方法

```bash
<script>
import { mapMutations, mapActions} from 'vuex'
export default {
    data(){
        return {
            val: this.$store.state.val
        }
    },
    methods:{
        ...mapMutations({setVal: 'changeVal'}),
        ...mapActions({async: 'asyncChangeVal'})
    },
    watch: {
        val(){
            this.async(this.val);
        }
    }
}
</script>
```
#### 敲黑板！！！
上面简单说了一下mutations、actions的运用场景，现在总结一下，mutations在异步操作的时候，在调式工具中，并不能保障**记录**提交的数据和当前的数据一致，而通过actions方法。可以异步的操作函数，同步的调用mutations的方法，从而确保调试的每一次数据变化一致，实现tracel time的旅行时间的数据变化

#### getters根据函数派生出新的值
以E为输出，D为接受来看一样在index.js设置
```bash
export default new vuex.Store({
    state:{
        val: 'hello'
    },
    mutations: {
        changeVal(state, newVal){
            // setTimeout(() => {
                state.val = newVal
            // }, 1000)            
        }
    },
    actions: {
        // ctx相当于vue.$store镜像
        asyncChangeVal(ctx, newVal){
            setTimeout(() => {
                ctx.commit('changeVal', newVal)
            }, 1000)
        }
    },
+   getters: {
        reviseVal(state){
            #派生出去的字符串一个个的
            return state.val.split('').join();
        }
    }
})
```
老规矩两种调用`revisVal`方法，因为D为输出组建所以在D设置
* 第一种  vue.$store.getters.reviseVal

```bash
<template>
    <div class="d-wrapper">
        <div>我是组建D</div>
# 这里这里这里
        <div>数据：{{this.$store.getters.reviseVal}}</div>
    </div>
</template>
```
* 第二种  


#### modules模块化
还是在index.js中设置,之前的内容上面有这里就不一一写了
```bash
export default new vuex.Store({
    state:{
        val: 'hello'
    },
    mutations: {
        changeVal(state, val){
            state.val = val;
        }
    },
    actions: {....},
    getters: {...}
    },
+   modules: {
        map: {
            # 创建命名空间
            namespaced: true,
            state: {
                val: 'map'
            },
            mutations: {
                changeVal(state, val){
                    state.val = val;
                }
            }
        },
        news: {}
    }
})
```
如果不创建命名空间，调用`changeVal`函数改变state中了val值，因为父级和子级map都具有changeVal函数，所以在其他模块调用`state.val`的时候会一同改变。所以需要添加**namespaced: true**，并且可以在E中指定调用changeVal函数
**调用父级的函数**
```bash
watch: {
    val(){
        this.$store.commit('changeVal', this.val);
    }
}
```
**调用map中函数**
```bash
watch: {
    val(){
        this.$store.commit('map/changeVal', this.val);
    }
}
```
**或者调用辅助函数如：mapMutations(map, {setVal: 'changeVal'}),**
辅助函数支持调用两个参数，第一个就是模块名