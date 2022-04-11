---
title: 2_vue3学习之路由的使用
categories: Vue
tags: vue3
date: 2022-01-12 14:40:00
---

# 1.了解3.x
Vue 3.x 的引入方式如下（其中 RouteRecordRaw 是路由项目的 TS 类型定义）。
```bash
import { createRouter, createWebHistory, RouteRecordRaw } from 'vue-router'

const routes: Array<RouteRecordRaw> = [
   {
    path: '/',
    name: 'home',
    component: () => import('@views/home.vue')
  }
]

const router = createRouter({
  history: createWebHistory(process.env.BASE_URL),
  routes
})

export default router
```

# 2.使用 route 获取路由信息
1.导入路由组件
```bash
import { useRoute } from 'vue-router'
```
2.定义路由变量
```bash
const route = useRoute();
```
3.读取路由信息
```bash
# 获取路由名称
console.log(route.name);

# 获取路由参数
console.log(route.params.id);
```

# 3.404路由页面配置
```bash
const routes: Array<RouteRecordRaw> = [
  {
    path: '/:pathMatch(.*)*',
    name: '404',
    component: () => import('@views/404.vue')
  }
]
```