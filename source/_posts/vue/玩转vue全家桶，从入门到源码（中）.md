---
title: 玩转vue全家桶，从入门到源码（中）
categories: Vue
tags: vue
date: 2018-07-19 10:20:00
---
# vue-router架构最流行的Spa面应用（上）
## 配置router环境
### 搭建cli
```bash
vue init webpack thr-router
```
### 在main.js中引入vue-router
```bash
import router from './router'
new Vue({
  el: "#app",
+ router,
  components: {
    App
  },
  template: '<App/>'
})
```
### 在src文件夹下router下创建idnex.js
```bash
import Vue from 'vue'
import VueRouter from 'vue-router'
Vue.use(VueRouter);

export default new VueRouter({

})
```
## 配置router文件（一） 路由后连接的`#`
### router下index.js文件
引进组件，在VueRouter实例下设置路由。
```bash
import home from '../components/home'
import doc from '../components/doc'
import about from '../components/about'
import err from '../components/err'
export default new VueRouter({
  routes: [
    {
      path: '/home',
      component: home
    },
    {
      path: '/doc',
      component: doc
    },
    {
      path: '/about',
      component: about
    },
    {
      path: '*',
      component: err
    }
  ],
  mode: 'history' # 没有 **#**
  #mode: 'hash'      有 **#**
})
```
### App.vue下插入路由标签
```bash
<template>
  <div id="app">
    Welcom to duyi
    <ul>
      <li><a href="#/home">home</a></li>
      <li><a href="#/doc">doc</a></li>
      <li><a href="#/about">about</a></li>
    </ul>
    <router-view></router-view>
  </div>
</template>
```
### 以home为例，建立的组件
```bash
<template>
    <div class="wrapper">
        <div class="content">
            我是{{title}}
            房间id{{$route.params.id}}
            成员id{{$route.params.mum_id}}
            #获取哈希值，在vue实例参数中
            #房间id{{$route.query.id}}
            #成员id{{$route.query.mum_id}}
        </div>
    </div>
</template>

<script>
export default {
    data(){
        return {
            title : 'home'
        }
    },
    # 页面的监听。to之后的路由，from上一个路由 
    watch: {
        $route(to, from){
            console.log(to, from);
        }
    }
}
</script>
```
## 配置router文件（二） router-link标签
### 在App.vue中设置，代替上面的a标签模板调转。但效果一样
```bash
<template>
  <div id="app">
    Welcom to duyi
    <ul>
      <li><router-link to="/home">home</router-link></li>
      <li><router-link to="/doc">doc</router-link></li>
      <li><router-link to="/about">about</router-link></li>
    </ul>
    <router-view></router-view>
  </div>
</template>
```
### router-link加强
```bash
<router-link :to="{name: 'home'}">home</router-link>
```
**routes** 中的改变
```bash
{
  path: '/home/:id?/number/:num_id?',
  name: 'home',
  component: home
}
```
### redirect 重定向  三种用法：字符、对象、函数对象
#### 所有没有的页面都通过redirect跳转所设置的页面
```bash
 {
  path: '*',
  # redirect: '/home'
  # redirect: {path: '/doc'}
  redirect(to){
    if(to.path == '/'){ #根目录 下自动跳转这个房间
      #restful风格
      return {name: 'home', params: {id: 666, mum_id: 777}}
      #no restful
      #return {name: 'home', query: {id: 666, mum_id: 777}}
      #此时要将hemo中的 routes => path: '/home',
    }else{
      return {name: 'err'};
    }
  }
}
```

* url 风格     home/23/number/12345
* restful风格  home/home_id/number/:id  =>  parame={home_id: 23, id: 1235}
* no restful   home?hemoid=123&id=456      =>   query = {homeid: 123, id: 456}

# vue-router架构最流行的Spa面应用（下）
## routes下的子路 以about为例创建子路由
### 在index.js下 routes中的改写

```bash
{
  path: '/about',
  name: 'about',
  component: about,
+ children: [
    {
      path: 'person',
      name: 'person',
      component: person
    },
    {
      path: 'hobbit',
      name: 'hobbit',
      component: hobbit
    },
    {
      path: 'blog',
      name: 'blog',
      component: blog
    }
  ]
},
```
### about下的`router-link`和`router-view`标签的扩展(一)
```bash
<template>
    <div class="wrapper">
        <div class="content">
            我是{{a}}
        </div>
+       <router-link to="/about/person" tag="p">person</router-link>
+       <router-link to="/about/hobbit" tag="p" >hobbit</router-link>
+       <router-link to="/about/blog" tag="p">blog</router-link>
+       <router-view></router-view>
    </div>
</template>
```
`router-link`默认渲染成a标签，通过`tag`属性修改标签，当子级`person`被点击之后会有class为**router-link-exact-active router-link-active**，**router-link-exact-active**表示当前被点击的class，父级`about`的class变成**router-link-active**表示当前活跃的标签,**其类名比较繁琐可设置，如下**
### linkActiveClass和linkExactActiveClass属性，在index.js中设置
```bash
export default new VueRouter({
  router: [...],
  mode: 'hash',
  linkActiveClass: 'active',     #当前活跃的class
  linkExactActiveClass: 'exact'  #当前被点击的class
})
```
### 同时衍生出来的router-link属性
```bash
<router-link :to="{name: 'home'}" tag="li" 
  active-class="active-home" 
  exact-active-class="exact-active"
>home</router-link>
```
`active-class`当前活跃状态下的标签会显示class，`exact-active-class`当前被点击下的标签会显示下的class，此属性只作用于所在的标签上。
## 一个组建下配置多个路由
### router->index.js中的routes设置，以doc为例
```bash
{
  path: '/doc/:id?',
  name: 'doc',
  components: {
    default: doc,   #默认路由
    top: home       #命名路由
  }
},
```
### App.vue中的标签设置
```bash
+ <router-view name="top"></router-view>
  <router-view></router-view>
```
`router-view`可以设置多个标签，然后通过name设置组建。有名按名没名默认

## 过度动效
### App.vue下设置,在reouter-view标签上包裹一层transition标签
```bash
<transition mode="in-out">
    <router-view class="bc"></router-view>
  </transition>
```
`mode`属性支持，**in-out**先进后出，**out-in**显出后进，以及默认值同时进行，三种动画效果模式，**style**设置如下：
```bash
.v-enter{
  transform: translateX(-100%);
}
.v-enter-to{
  transform: translateX(0);
}
.v-leave{
  transform: translateX(0);
}
.v-leave-to{
  transform: translateX(-100%);
}
.v-enter-active, .v-leave-active{
  transition: all 1s;
}
```
具体功能要求不就做过多解释，之前笔记有详细讲解。
## 导航守卫
* 针对于路由对象(new VueRouter())的守卫  beforEach
* 针对单个路由(route数组中的每个路由)的守卫  beforEnter
* 针对组件(component)的守卫  beforeRouteEnter

#### 功能：添加一个login登录组建，只有在login点击登录才能访问doc内容，下面以三种守卫为例实现此功能
### login.vue通用设置
```bash
<template>
  <div class="wrapper">
    <button @click="login">点击登录</button>
  </div>
</template>
<script>
export default {
  methods: {
      login(){
          var doc = this.$router.options.routes.filter(item => item.name == 'doc')[0];
          console.log(doc);
          doc.meta.isLogin = true;
      }
  }
}
</script>
```
### 路由对象beforEach应用
#### 在router下index.js中操作
```bash
const router = new VueRouter({...})
router.beforeEach((to, from, next) = > {
  if(to.path == '/doc'){
    if(to.meta.isLogin){
      next();
    }else{
      next({path: '/login'})
    }
  }else{
    next();
  }
})
```
`to`打印的去的路由信息，`from`表示起始出发的路由信息，`next`是执行函数，只有执行，才会从`from`跳转到`to`,由此可以利用from和to条件进行判断是否跳转。
### 在单个路由befotEnter应用
#### 在index.js中操作
只有当点击doc的时候才会触发
```bash
const router = new VueRouter({
  {
    path: '/doc',
    name: 'doc',
    component: doc,   # 默认路由
+   beforeEnter(to, from, next){
     console.log(to, from);
      if(to.meta.isLogin){
        next();
      }else{
        next({path: '/login'});
      }
    },
    meta: {
      index: 2,
+     isLogin: false
    }
  },
})

```
### 组件的守卫  beforeRouteEnter
#### 以dec.vue为例
```bash
<script>
export default {
    data(){
        return {
            title : 'doc'
        }
    },
    beforeRouteEnter(to, from, next){
        if(to.meta.isLogin){
            next(vm => vm.title = 'newDoc');
        }else{
            next({path: '/login'});
        }
        console.log(this)  # this => undefined
        # vm 为组建实例只有通过这样才能调用实例
    }
}
</script>
```
## 路由懒加载 index.js下配置路由
### 路由懒加载之前
```bash
import home from '../components/home'
const router = new VueRouter({
  routes: [
    {
      path: '/home',
      name: 'home',
      component: home,
      meta: {
        index: 1
      }
    }
  ]
})
```
### 路由懒加载之后
```bash
const router = new VueRouter({
  routes: [
    {
      path: '/home',
      name: 'home',
      component: ()=> import('../components/home'),
      meta: {
        index: 1
      }
    },
  ]
})
```

## router.js配置
```bash
import Vue from 'vue'
import Router from 'vue-router'
# import Home from './views/Home.vue'
import About from './views/About.vue'
import Learn from './views/Learn.vue'
import Student from './views/Student.vue'
import CommunityCenter from './views/CommunityCenter.vue'

import Academic from './components/community/Academic.vue'
import Download from './components/community/Download.vue'
import Personal from './components/community/Personal.vue'
import Question from './components/Question.vue'
import Err from './components/Err.vue'

const Home = () => import('./views/Home')

Vue.use(Router)

const router =  new Router({
  mode: 'history',  # 取消url中的 ‘/#/’
  base: process.env.BASE_URL,
	linkActiveClass: 'active',
	linkExactActiveClass: 'exact',
  routes: [
    {
      path: '/home',
      name: 'home',
	    alias: '/',  # 路由别名，访问 / 和 /home 都可
      components: {    # home下有两个router-view
      	default: Home,
	      # 'academic': Academic
      }
    },
    {
      path: '/about',
      name: 'about',
      component: About
    },
    {
      path: '/learn',
      name: 'learn',
      component: Learn
    },
    {
      path: '/student',
      name: 'student',
      component: Student
    },
	  {
		  path: '/community',
		  name: 'community',
		  component: CommunityCenter,
		  redirect: '/community/academic', # 重定向
		  meta: {
			  login: false
		  },
		  children: [
			  {
			  	path: 'academic',
				  name: 'academic',
				  component: Academic,
				  # 路由独享守卫
				  # beforeEnter (to, from, next) {
					#   const answer = confirm('你还没有登录，要登录后才能浏览信息，要登录嘛？')
					#   if (answer) {
					# 	  next({name: 'personal'})
					#   } else {
					# 	  next(false)
					#   }
				  # }
			  },
			  {
			  	path: 'download',
				  name: 'download',
				  component: Download,
			  },
			  {
			  	path: 'personal',
				  name: 'personal',
				  component: Personal,
			  }
		  ]
	  },
	  {
	  	path: '/question/:id',
		  name: 'question',
		  component: Question
	  },
	  {
	  	path: '/err',
		  name: 'err',
		  component: Err
	  },
	  {
	  	path: '*',
		  redirect(to) {
	  		if (to.path == '/') {
	  			return '/home'
			  } else {
	  			return {name: 'err'}
			  }
		  }
	  }
  ]
})

# 全局守卫
# 1.全局守卫
# router.beforeEach( (to, from, next) => {
# 	# to 要到那里去
# 	# from 从哪来去
# 	if (to.path === '/community/academic') {
# 		const answer = confirm('你还没有登录，要登录后才能浏览信息，要登录嘛？')
# 		if (answer) {
# 			next({name: 'personal'})
# 		} else {
# 			next(false)
# 		}
# 	} else {
# 		next()
# 	}
# })

export default router
```