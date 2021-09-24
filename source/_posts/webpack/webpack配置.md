---
title: webpack4配置
categories: webpack
tags: webpack
date: 2018-04-13 09:10:00
---

# 配置webpack

## 全局下载webpack
```bash
npm install webpack -g
```
注意：下载到的文件夹必须是英文的，且不是包含webpack字段，如webpack1等，路径也都是英文的，
## 文件夹中初始化
```bash
npm init
```
可以一路向下回车，用于表示项目的，之后会出现一个`package.josn`文件
## 在文件下局部下载webpack
```bash
npm install webpack --save-dev
```
创建开发环境

## 如果上传文件的时候并不需要将node_modules也传过去
```bash
npm install
```
执行上面的命令会之间下载josn中所需要配置的所有文件。

## 下载webpack-cli
```bash
npm install webpack-cli -g
```
webpack3版本之前的下载webpack会自动下载webpack-cli，webpack改版之后需要自行安装

## 执行webpack
```bash
webpack 
```
执行webpack前需要新建入口文件，在文件夹下新建`src`文件夹下新建`js`文件，之后在执行webpack就生成一个出口文件`dist`

## 模块化开发
### src中两个文件js，
#### 主函数index.js
```bash
var demo = require('./demo.js');
demo.add(22, 33);
```
#### 附属函数
```
module.exports = {
    add: function(a, b){
        console.log(a + b);
    }
}
```
## webpack.config.js的配置
### 打包压缩图片需要安装两个load
```bash
npm install url-loader --save-dev
```
```bash
npm install file-loader --save-dev
```
### 现webpack.config.js初始的配置
```bash
module.exports = {
    #入口文件路径
    entry: {
        demo: './src/demo.js',
        index: './src/index.js'
    },  
    output:{
        path: __dirname + '/dist',      #出口文件夹路径
        filename: '[name].js',         #出口文件路径
        publicPath: './dist'            #静态资源路径如图片
    },
    module: {
        rules: [
            #图片文件的压缩配置
            {test: /.jpg|.png$/,use:['url-loader?limit=1000&name=./[name].[ext]']}
        ]
    },
    mode: 'development'   #mode表示设置当前环境,如development为开发环境
}
```
### css及less的使用
```bash
 module: {
    rules: [
        {test: /.less$/, use:['style-loader', 'css-loader','less-loader']}
    ]
},
```
module数组中添加上述命令，之后安装上面三个loader，之后还需在安装一个`npm install less --save-dev`


### 安装插件的配置
```bash
+ var uglifyjs = require('uglifyjs-webpack-plugin')

module.exports = {
    #入口文件路径
    entry: {
        demo: './src/demo.js',
        index: './src/index.js'
    },  
    output:{
        path: __dirname + '/dist',      #出口文件夹路径
        filename: '[name].js',         #出口文件路径
        publicPath: './dist'            #静态资源路径如图片
    },
    #loader
    module: {
        rules: [
            #图片文件的压缩配置
            {test: /.jpg|.png$/,use:['url-loader?limit=1000&name=./[name].[ext]']}
        ]
    },
    plugins:[
    +   new uglifyjs()
    ],
    mode: 'development'  #mode表示设置当前环境,如development为开发环境
}
```
如上面没配置问一样，需要什么插件在顶上`require`出来，下面`new`出来

