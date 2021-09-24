title: vue之服务器渲染初探     
categories: Vue
tags: vue ssr服务器端渲染
date: 2018-11-3 10:45:14 
---
# 为什么要用ssr呢
## 服务器端渲染的发展史
```bash
* 1990年产生万维网，学术第一个浏览器，编译器 web服务器
* 1994年创建万维网联盟，在麻省理工建立了计算机研究室
* 用途：方便资料快速传输，方便大家看
* 1994 大众认知的 第一个浏览器“网景导航”
* 问题： 
    - 所有页面只有html，静态页面
    - 页面的相关运算，都在服务器端进行 
* 1994产生PHP Person Home Page（超文本预处理器）
* 模板引擎
    - <p>{}</p> 魔板字符串
* 1995 javascript 用于客户端进行计算的脚步
* 1999 异步传输对象 ActiveX XMHttpRequst
* Web2.0 异步加载数据
* 前端是由后端分生出来的职业
* 2008 Chrom谷歌浏览器的出现，浏览器页面运算速度超快
* 现在的前端
    - 通过Ajax请求数据，根据特定逻辑生成对应的DOM页面
    - 写哈数据库，做好接口
* 2009 Node的出来
```
## 开始我们的代码
### 初始化
```bash
npm install
```
### 下载vue及vue-server-renderer
```bash
cnpm install vue -vueserver-renderer --save
```
### 创建demo.js配置文件
```bash
var Vue = require('vue');
var ServerRenderer = require('vue-server-renderer');
var http = require('http');
# 创建Vye实例
var app = new Vue({
    template: `<div class="wrapper">
                <show-msg></show-msg>
                <div>
                    <input v-model="val">
                    <div>{{val}}</div>
                </div>
               </div>`,
    data: {
        val: 'you were, are an will be in my heart'
    },
    components: {
        showMsg: {
            template: `<h1>{{value}}</h1>`,
            data() {
                return {
                    value: 'welcome 2 tydk'
                }
            }
        }
    }
})
# 创建Render实例（将vue实例转化成html字符串）
var renderer = ServerRenderer.createRenderer({
    # 创建一个html模板
    template: require('fs').readFileSync('./index.template.html', 'utf-8')
})
// ddd
# 跑服务
http.createServer((req, res) => {
    # 通过renderToString（Vue实例）转化成HTML字符串
    renderer.renderToString(app, {
        start: '<script>console.log('start')</script>',
        end: '<script>console.log('end')</script>'
    }, (err, html) => {
        res.end(html) # 将 Vue 实例渲染为字符串html
    })
}).listen('3000');
```

### 创建index.template.html的模板html
**<!--vue-ssr-outlet-->**
```bash
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>I am is a template for SSR</h1>
    <!--vue-ssr-outlet-->
</body>
</html>
```