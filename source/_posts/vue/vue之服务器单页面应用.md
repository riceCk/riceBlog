title: vue之服务器单页面应用     
categories: Vue
tags: vue ssr服务器端渲染
date: 2018-11-3 10:45:14 
---

# 单页面操作
## 前期部署
### 初始化
```bash
npm init
```
### vue + vue-server-renderer + express
```bash
cnpm install vue vue-server-renderer express --save
```
## vue-ssr服务器搭建
```bsah
|- entry
|   |- entry-server.js
|   |- entry-client.js
|- src
|   |- components
|   |   |- home.vue
|   |   |- about.vue
|   |- App.vue
|   |- main.js
|   |- router.js
|   |- store.js
|- webpack
|   |- webpack.server.js
|   |- webpack.client.js
|- index.template.html
|- package,json
|- server.js
```
### entry-server.js入口文件
```bash
import createApp from '../src/main.js'

export default (config) => {
    return new Promise((resolve, reject) => {
        let app = createApp()
        app.$router.push(config.url)
        let matchComponents = app.$router.getMatchedComponents()
        if (matchComponents.length == 0 ) {
            return reject({code: 404})
        }
        Promise.all(matchComponents.map(component => {
            if (component.serverRequest) {
                return component.serverRequest(app.$store)
            }
        })).then(() => {
            config.state = app.$store.state
            resolve(app)
        })
    })
}
```

### entry-client.js
```bash
import createApp from '../src/main.js'

 let app = createApp()
if (window.__INITAL__STATE__) {
    app.$store.replaceState(window.__INITAL__STATE__)
}

window.onload = function () {
    app.$mount('#App')
}
```

### home.vue
```bash
<template>
    <div class="homeWrapper">
        <div class="title">{{mas}}</div>
        <div class="content">Store: {{value}}</div>
        <button @click="getValue">getValue</button>
    </div>
</template>

<script>
    export default {
        serverRequest(store) {
            return store.dispatch('getValue')
        },
        created() {
            this.$store.dispatch("getValue")
        },
        computed: {
            value() {
                return this.$store.state.value
            }
        },
        data() {
            return {
                mas: 'home'
            }
        },
        methods: {
            getValue() {
                this.$store.dispatch("getValue")
            }
        }
    }
</script>

<style scoped>

</style>
```

### App.vue 主组件
```bash
<template>
    <div id="App">
        <p>我是组件入口文件</p>
        <router-view></router-view>
    </div>
</template>

<script>
    export default {

    }
</script>

<style>
    #App {
        color: red
    }
</style>
```

### main.js 工厂函数创建vue实例
```bash
import Vue from 'vue'
import App from './App.vue'
import createRouter from './router.js'

export default function createApp() {
    return new Vue({
        router: createRouter(),
        render: createElement => createElement(App)
    })
}
```

### router.js路由配置
```bash
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
import home from './components/home.vue'
import about from './components/about.vue'
import show from './components/show.vue'

export default function () {
    return new VueRouter({
        mode: 'history',
        routes: [
            {
                path: '/',
                component: home
            },
            {
                path: '/about',
                component: about
            },
            {
                path: '/show',
                component: show
            }
        ]
    })
}
```

### store.js 
```bash
import Vue from 'vue'
import Vuex from 'vuex'
import axios from 'axios'

Vue.use(Vuex)

export default function () {
    return new Vuex.Store({
        state: {
            value: '111'
        },
        mutations: {
            setValue(state, value) {
                state.value = value
            }
        },
        actions: {
            getValue({commit}) {
                return axios.get('http://localhost:3000/api/getValue').then(value => {
                    commit('setValue', value.data)
                })
            }
        },
    })
}
```

### webpack.server.js 服务器配置
```bsah
const path = require('path')
const root = path.resolve(__dirname, '..')
const vueLoaderPlugin = require('vue-loader/lib/plugin')

module.exports = {
    target: 'node',
    mode: 'development',
    entry: ['babel-polyfill', path.join(root, 'entry/entry-server.js')],
    output: {  // 输出
        libraryTarget: 'commonjs2',
        path: path.join(root, 'dist'),
        filename: 'bundler.server.js'
    },
    module: { // 模块
        rules: [
            {
                test: /\.vue$/,
                loader: 'vue-loader'
            },
            {
                test: /\.js$/,
                loader: 'babel-loader',
                include: root,
                exclude: /node_modules/
            },
            {
                test: /\.css$/,
                loader: 'vue-style-loader!css-loader'
            }
        ]
    },
    plugins: [new vueLoaderPlugin()] // 插件
}
```

### webpack.client.js 页面进行挂在配置
```bash
const path = require('path')
const root = path.resolve(__dirname, '..')
const vueLoaderPlugin = require('vue-loader/lib/plugin')

module.exports = {
    mode: 'development',
    entry: ['babel-polyfill', path.join(root, 'entry/entry-client.js')],
    output: {  // 输出
        path: path.join(root, 'dist'),
        filename: 'bundler.client.js'
    },
    module: { // 模块
        rules: [
            {
                test: /\.vue$/,
                loader: 'vue-loader'
            },
            {
                test: /\.js$/,
                loader: 'babel-loader',
                include: root,
                exclude: /node_modules/
            },
            {
                test: /\.css$/,
                loader: 'vue-style-loader!css-loader'
            }
        ]
    },
    plugins: [new vueLoaderPlugin()] // 插件
}
```

### index.template.html
```bash
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    {{{bind}}}
</head>
<body>
    <h1>vue ssr template</h1>
    <!--vue-ssr-outlet-->
    {{{init}}}
</body>
</html>
```

### package.json配置
```bash
{
  "name": "ssr",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@babel/core": "^7.0.0",
    "axios": "^0.18.0",
    "babel-loader": "^8.0.4",
    "babel-polyfill": "^6.26.0",
    "css-loader": "^1.0.1",
    "express": "^4.16.4",
    "vue": "^2.5.17",
    "vue-loader": "^15.4.2",
    "vue-router": "^3.0.1",
    "vue-server-renderer": "^2.5.17",
    "vue-template-compiler": "^2.5.17",
    "vuex": "^3.0.1",
    "webpack": "^4.24.0"
  },
  "devDependencies": {},
  "description": ""
}
```

### server.js 后端node+express配置
```bash
const express = require('express')
const serverRender = require('vue-server-renderer')
const createApp = require('./dist/bundler.server.js')['default']
const clienturl = '/bundler.client.js'
const server = express()
server.use('/', express.static(__dirname + '/dist'))
const renderer = serverRender.createRenderer({
    template: require('fs').readFileSync('./index.template.html', 'utf-8')
})
server.get('/api/getValue', (req, res) => {
    res.end('hello') // 模拟后端传到hell字段值
})
server.get('*', (req, res) => {
    let config = {url: req.url}
    createApp(config).then(app => {
        let state = JSON.stringify(config.state)
        renderer.renderToString(app,  {
            init: `<script src=${clienturl}></script>`,
            bind: `<script>window.__INITAL__STATE__ = ${state}</script>`
}, (err, html) => {
            res.end(html)
        })
    })
})

server.listen(3000, () => {
    console.log(9999)
})
```
