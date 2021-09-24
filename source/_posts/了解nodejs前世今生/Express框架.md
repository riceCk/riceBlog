---
title: 详解express框架
categories: 了解nodejs前世今生
tags: node
date: 2018-08-27 08:42:14  
---
# 安装express
```bash
npm install express --save
```
# hello world 实例
```bash
var express = require('express');
var app = express();

app.get('/', function (req, res) {
  res.send('Hello World!');
});

app.listen(3000, function () {
  console.log('Example app listening on port 3000!');
});
```
# 基本路由
## GET请求进行响应
```bash
app.get('/', function (req, res) {
  res.send('Hello World!');
});
```
## 对 POST 请求进行响应
```bash
app.post('/', function (req, res) {
  res.send('Got a POST request');
});
```
## get请求进行响应
```bash
app.put('/user', function (req, res) {
  res.send('Got a PUT request at /user');
});
```
## get请求进行响应
```bash
app.delete('/user', function (req, res) {
  res.send('Got a DELETE request at /user');
});
```
# 静态文件路径
```bash
app.use(express.static('public'));
```
文件放在根路径上，但是一般我们不会吧文件都放在根路径下，此时就可以**映射**。
```bash
app.use('/statice',express.static('public'));
```
将文件映射到`statice`目录下

# express脚手架
```bash
npm install express-generator -g
express -e
```
## 路由路径
### 基于字符串模式的路由路径的一些示例
字符 ?、+、* 和 () 是其正则表达式同应项的子集。基于字符串的路径按字面理解连字符 (-) 和点 (.)。
#### 此路由路径将匹配 acd 和 abcd。
```bash
app.get('/ab?cd', function(req, res) {
  res.send('ab?cd');
});
```
#### 此路由路径将匹配 abcd、abbcd、abbbcd 等。
```bash
app.get('/ab+cd', function(req, res) {
  res.send('ab+cd');
});
```
#### 此路由路径将匹配 abcd、abxcd、abRABDOMcd、ab123cd 等。
```bash
app.get('/ab*cd', function(req, res) {
  res.send('ab*cd');
});
```
#### 此路由路径将匹配 /abe 和 /abcde。
```bash
app.get('/ab(cd)?e', function(req, res) {
 res.send('ab(cd)?e');
});
```
#### 基于正则表达式的路由路径的示例
此路由路径将匹配 butterfly 和 dragonfly，但是不匹配 butterflyman、dragonfly man 等。
```bash
app.get(/.*fly$/, function(req, res) {
  res.send('/.*fly$/');
});
```
### 路由处理程序
#### 回调
支持多个回调函数可以处理一个路由（确保您指定 next 对象）
```bash
app.get('/example/b', function (req, res, next) {
  console.log('the response will be sent by the next function ...');
  next();
}, function (req, res) {
  res.send('Hello from B!');
});
```
#### 一组回调函数可以处理一个路由
```bash
var cb0 = function (req, res, next) {
  console.log('CB0');
  next();
}
var cb1 = function (req, res, next) {
  console.log('CB1');
  next();
}
var cb2 = function (req, res) {
  res.send('Hello from C!');
}
app.get('/example/c', [cb0, cb1, cb2]);
```
## 中间件
### 中间件函数可以执行以下任务
* 执行任何代码。
* 对请求和响应对象进行更改。
* 结束请求/响应循环。
* 调用堆栈中的下一个中间件

### Express 应用程序可以使用以下类型的中间件
* 应用层中间件
* 路由器层中间件
* 错误处理中间件
* 内置中间件
* 第三方中间件

#### 应用层中间件
```bash
app.use(function (req, res, next) {
  console.log('Time:', Date.now());
  next();
});   
app.use('/user/:id', function (req, res, next) {
  console.log('Request Type:', req.method);
  next();
});
app.get('/user/:id', function (req, res, next) {
  res.send('USER');
});
```