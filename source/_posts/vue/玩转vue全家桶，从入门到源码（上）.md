---
title: 玩转vue全家桶，从入门到源码（上）
categories: Vue
tags: vue
date: 2018-07-19 09:47:00
---
# Vue简单的指令(一)
## html标签
```bash
<div id="app">
    {{msg}}
    <div v-text="msg"></div>        #渲染div内容
    <div v-html="htmlVal"></div>    #渲染dom，尽量不使用，影响性能
    <div v-once>{{msg}}</div>       #至渲染一次，之后msg动态改变不进行渲染
    <div>{{usd}}</div>              #可以通过computed计算属性进行执行
    <div class="content" v-on:click="bind">111</div> #点击事件
    <button @click="e => {val++}">+</button>  #支持事件源对象参数和语句中运行函数
    <span>{{val}}</span>
    #点击修饰符，.right是右击 .left是左击  .prevent是取消默认事件 .shop阻止事件冒泡
    <button @click.right.prevent="e => {val--}">-</button>
    # .enter回车键事件等键盘按键修饰符
    <input type="text" @keyup.enter="show">    
    # v-bind的用法(绑定)既可以绑定数组也可以绑定对象
    <div v-bind:class="cont" :style="[styles,{fontWeight: 600}]">safas</div>

</div>
```
## js脚本
```bash
var vm = new Vue({
    el: "#app",
    data: {
        msg: "hello Vue",
        htmlVal: '<span>asd</span>',
        money: 8,
        val: 0,
        cont: "content",
        styles:{color: 'black',fontSize:'32px'}
    },
    methods:{
        bind(){
            console.log('conten is click')
        },
        show(e){
            console.log(e)
        }
    },
    computed: {  #计算属性
        usd(){
            return this.money * 6
        }
    },
    watch: {    #监听属性
        #监听什么属性就写什么属性，参数接受新值和改变前值
        msg(newVal, oldVal){
            console.log(newVal, oldVal);
        }
    }
})
```
# Vue简单的指令(二)
## v-if和v-show的区别
### html标签
```bash
<div id="app">
    <div class="content red" v-if="num%2 == 0"></div>
    <div class="content yellow" v-else-if="num%3 == 0"></div>
    <div class="content green" v-else></div>
    <div class="content blue" v-show="flag"></div>
</div>
```
当v-if条件为false的时候页面中是没有相关的dom标签的，当发生变化之后进行重排重会，很耗费性能，v-shwo则是动态的添加了一个dispaly属性。
### js脚本
```bash
 var vm = new Vue({
    el: "#app",
    data: {
        num: 2,
        flag: false
},     
```
# Vue组件化开发
## 创建全局组建
```bash
<div id="app">
      <my-com></my-com>
</div>
<script>
    #创建全局组建
    Vue.component('myCom', {
        template: `<div>hello</div>`,
        data,
        method,
        component,
    })
    var vm = new Vue({
        el: "#app",
        data: {
        }
    })
</script>
```
## 创建局部组建
### computer属性 方法/属性
```bash
<div id="app">
    <my-com></my-com>
</div>
<script>
    #局部组建
    const myCom = {
        template: `
                <div>
                    <div>{{myShow}}</div>
                    <button @click="change">changeshow</button>
                </div>
        `,
        data(){
            return{
                show: "hello world"
            }
        },
        methods:{
            change(){
                this.myShow = 'ke.qq.com'
            }
        },
        #计算属性
        computed: {
            #计算属性是已有属性得出的值，只能改变已有属性
            myShow: {
                get(){
                    return this.show + 'welcome to duyi'
                },
                set(newVal){
                    this.show = newVal; 
                }
            }
        }
    }
    var vm = new Vue({
        el: "#app",
        components: {
            myCom
        },
        data:{
            msg: 'dingyonyi '
        }
    })
</script>
```
上面已经说过了computed的属性就是函数类型的使用，这里就不多说了。当需要改变computed中的属性的时候只能通过方法`get`和`set`暴露出来。此例题就是讲的通过点击改变内容

### props用法
```bash
<div id="app">
            # :msg 是自定义的，'msg'是data中的属性。
    <my-com :msg="msg"></my-com>
</div>
<script>
    const myCom = {
        props:['msg'],
        template: `
                <div>
                    <div>{{msg}}</div>
                    <div>{{newMsg}}</div>
                    <button @click="change">changeshow</button>
                </div>
        `,
        data(){
            return{
                show: "hello world",
                #props从父级传过来之后重新赋值，方便后期子级修改
                newMsg: this.msg
            }
        },
        methods:{
            change(){
                this.newMsg = 'Son'
            }
        },
    }
    var vm = new Vue({
        el: "#app",
        components: {
            myCom
        },
        data:{
            msg: 'Farther'
        }
    })
</script>
```
Vue是一种**原始值单向数据流**，父级的数据改变能引发子级的数据改变。但是子级的变化不会引发父级的改变,这是限于`原始值`之间的父级传递**单项数据流**，但是`引用值`就是**双向**的，因为你改变的是地址
### props对象类型限制
```bash
 props:{
    msg: {
        #限制类型
        type: String,
        # 是否必须传值
        required: true
    },
    info:{
        type: String,
        #默认值
        default: 'info',
        # 校验
        validator (value) {
            console.log(value); # value就是传到值
            return true # 最后必须返回turue
        }
    }
},
```
`type`限制所传数据类型的，`default`默认值，模块上并没有传值，但子级类用上的就走默认值，`required`限制模块上必须传值
### 自定义事件，将子级数据传输到父级
```bash
<div id="app">
    <div>Son -> {{sonValue}}</div>
    <my-com @tellme="tell" ></my-com>
</div>
<script>
    const myCom = {
        template: `
                <div>
                    <input type="text" v-model="value"></input>
                </div>
        `,
        data(){
            return{
                value: ''
            }   
        },
        watch: {
            value(){
                console.log(this.value);
                #触发当前组建上的事件
                this.$emit('tellme', this.value);
            }
        }
    }
    var vm = new Vue({
        el: "#app",
        components: {
            myCom
        },
        data:{
            sonValue: ''
        },
        methods:{
            #value参数是 this.$emit('tellme', this.value);第二个参数
            tell(value){
                this.sonValue = value
            }
        }
    })
</script>
```
利用子级自定义事件将参数传到父级中，在通过父级改变自身的值从而间接的子级改变父级参数
### 实例简单的表单匹配
```bash
<div id="app">
    #下面两句话是相等的意思
    <my-inp v-bind="bindmsg"></my-inp>
    #<my-inp :title="title" :reg="reg" :errmsg="errmsg" :sucmsg="sucmsg"></my-inp>
</div>
<script>
    const myInp = {
        template: `<div>
            <span>{{title}}</span>
            <input type="text" v-model="val"></input>
            <span>{{msg}}</span>
        </div>`,
        data(){
            return {
                val: ''
            }
        },
        props:{
            title: {
                default: '',
                type: String
            },
            reg:{
                type: RegExp,
                default: null
            },
            errmsg: {
                type: String,
                default: 'NO'
            },
            sucmsg: {
                type: String,
                default: 'YES'
            }
        },
        computed: {
            msg(){
                if(!this.val){
                    return ''
                }
                return this.reg.test(this.val) ? this.sucmsg : this.errmsg;
            }
        }
    }
    new Vue({
        el: "#app",
        components: {
            myInp
        },
        data:{
            bindmsg: {
                title: "please input QQ Email",
                errmsg: "请重新输入",
                sucmsg: "正确",
                reg: /@qq.com$/
            }
        }
    })
</script>
```
当需要传的参数过多的话，可以定一个对象
### 普通插槽用法
```bash
<div id="app">
    <my-component>
        #<div slot="left">left</div>
        #<div slot="right">right</div>
        #content
    </my-component>
</div>
<script>
    const myComponent = {
        template: `
            <div>
                <div>myComponent</div>
                <div class="left">
                    <slot name="left">left slot</slot>
                </div>
                <div class="right">
                    <slot name="right">right slot</slot>
                </div>
                <div>
                    <slot> other slot</slot>
                </div>
            </div>
        `
    }
    var vm = new Vue({
        el: '#app',
        components:{
            myComponent
        }        
    })
</script>
```
可以通过给**slot**加名字来指定插槽的位置，同时支持默认值
### 作用域插槽
```bash
<div id="app">
    <my-component :lists="lists">
        #子组件slot将list传到prop中
        <li slot-scope="prop">{{prop.list.title}}</li>
    </my-component>
</div>
<script>
    const myComponent = {
        template: `
            <div>
                <div>myComponent</div>    
                <div>
                    <ul>
                        # 将list在传下它的子组件上
                        <slot v-for="list in lists" :list="list">{{list.title}}</slot>
                    </ul>
                </div>                
            </div>
        `,
        props:['lists']
    }
    var vm = new Vue({
        el: '#app',
        components:{
            myComponent
        },
        data:{
            lists: [
                {title: '吃饭'},
                {title: '睡觉'},
                {title: '打豆豆'},
                {title: '抽烟'},
                {title: '喝酒'},
                {title: '烫头'},
            ]
        }
        
    })
    </script>
```
### 动态组建
```bash
<div id="app">
    #缓存组建上的数据
    <keep-alive>
        #动态组建
        <component :is="comname"></component>
    </keep-alive>
    <button @click="comname = 'userName'">userName</button>
    <button @click="comname = 'passWord'">passWord</button>
</div>
<script>
    const userName = {
        template: `
            <div>
                userName: <input />
            </div>
        `
    }
    const passWord = {
        template: `
            <div>
                passWord: <input type="password" />
            </div>
        `
    }
    var vm = new Vue({         
        el: "#app",
        data:{
            comname: 'userName'
        },
        components: {
            userName,
            passWord
        }  
    })
</script>
```
### 过渡动画
#### 样式
```bash
<style>
    # 进入初始状态
    .v-enter{
        transform: translateX(-100%);
    }
    # 进入的末端状态
    .v-enter-to{
        transform: translateX(0);
    }
    # 进入的过程
    .v-enter-active{
        transition: all .5s ease-in;
    }
    # 离开起始
    .v-leave{
        transform: translateX(0);
    }
    # 离开末端
    .v-leave-to{
        transform: translateX(-100%);
    }
    # 离开过程
    .v-leave-active{
        transition: all .5s;
    }
</style>
```
#### 结构+行为
```bash
<div id="app" class="demo">
    <transition mode='out-in' name="left">
        <component :is="comname"></component>
    </transition>
    <button @click="comname = (comname == 'userName') ? 'passWord' : 'userName'">
        change
    </button>
</div>
<script>
    const userName = {
        template: `
            <div>
                userName: <input />
            </div>
        `
    }
    const passWord = {
        template: `
            <div>
                passWord: <input type="password" />
            </div>
        `
    }
    var vm = new Vue({         
        el: "#app",
    data:{
        comname: 'userName'
    },
    components: {
        userName,
        passWord
    }  
    })
</script>
```
Vue提供的6种过渡动画类名，可直接使用

* v-enter
* v-enter-to
* v-enter-active
* v-leave    刚要离开
* v-leave-to 离开到哪
* v-leave-active

`transition`支持**mode**属性，决定多个物体运动的时候运动顺序，并且支持**name**,可以把上面默认的`v-`变成**name**的值。
* in-out 先进后出
* out-in 先出后进
