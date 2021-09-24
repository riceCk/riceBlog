---
title: React前期配置
categories: React
tags: react
date: 2018-07-29 10:28:00
---

## 搭建react环境
```bash
npx create-react-app my-app
```

## sass的css配置
### 安装sass
```bash
npm install --save node-sass-chokidar
```
注：可能由于网络原因下载不了,需要利用淘宝镜像
```bash
cnpm install --save node-sass-chokidar
```
#### 配置package.json文件
```bash
 "scripts": {
+    "build-css": "node-sass-chokidar src/ -o src/",
+    "watch-css": "npm run build-css && node-sass-chokidar src/ -o src/ --watch --recursive",
     "start": "react-scripts start",
     "build": "react-scripts build",
     "test": "react-scripts test --env=jsdom",
```
#### 编译scss文件
```bash
npm run build-css
```
#### 实施检测scss文件
但是只是编译scss，并没有并行start页面的检测
```bash
npm run watch-css
```
#### 安装插件同时运行scss和start
```bash
cnpm install --save npm-run-all
```
####  配置package.json文件
```bash
 "scripts": {
     "build-css": "node-sass-chokidar src/ -o src/",
     "watch-css": "npm run build-css && node-sass-chokidar src/ -o src/ --watch --recursive",
-    "start": "react-scripts start",
-    "build": "react-scripts build",
+    "start-js": "react-scripts start",
+    "start": "npm-run-all -p watch-css start-js",
+    "build-js": "react-scripts build",
+    "build": "npm-run-all build-css build-js",
     "test": "react-scripts test --env=jsdom",
     "eject": "react-scripts eject"
   }
```

## 经过上面的配置，日常运行
```bash
npm run start
```

## Normalize.css
对页面的样式统一的设置
### 下载
![Normalize.css](https://necolas.github.io/normalize.css/8.0.0/normalize.css)

## react 特点
* 声明式的视图渲染
* 基于组件
* 多平台

## react语法：JSX语法先熟悉熟悉
```bash
const a = Array.from({length: 10}, (v, i) => i);      
const isRequired = true;
const text = 'hello react';
ReactDOM.render(
    <h1 className="test" onClick={()=>{alert('react')}}>{isRequired && text}</h1>,
    document.getElementById('example')
)
```
react属于逻辑内聚的，可以在属性中写事件绑定。

## React 组件 
* FuncComponent
* ClassCompoennt
    state  参数
    method 方法
    lifecycle生命周期

### FuncComponent
 ```bash
const FuncComponent = (props) => {
    return <div>hello,{props.name}</div>
}
ReactDOM.render(
    <FuncComponent name="world" />,
    document.getElementById('example')
)
```
### ClassCompoennt
```bash
class ClassCompoennt extends React.Component{
    state = {
        color: 'red'
    }
    methodA = () =>{
        
    }
render(){
    return (
        <div style={{color: this.state.color}}>
        hello,world
        </div>
    )
    }
}  
ReactDOM.render(
    <ClassCompoennt />,
    document.getElementById('example')
)
```

##  react的生命周期
```bash
class ClassCompoennt extends React.Component{
    state = {
        color: 'red'
    }
    methodA = {

    }
    #在render渲染前调用，在客户端也在服务端
    #可以在这方法中调用setTimeout或者ajax
    componentWillMount = () =>{
        this.setState({
            color: 'green'   #所以最后的渲染结果是绿色
        })
    }
    #在render之后第一次渲染后调用
    componentDidMount = () => {        
        #可以获取dom，这个期间已经渲染到页面中了
        console.log(document.getElementById('childElement'))
    }
    #组件接收到新的prop时调用，监控
    componentWillReceiveProps = () => {

    }
    #返回布尔值，在组件接收到props、state时被调用，可以确认不需要更新组件时使用，
    shouldComponentUpdate = () => {
    
    }
    #在组件接收到新到prop或者state但还没有render时被调用
    #不能调用setState，主要作用是更新prop或srate页面数据
    componentWillUpdate = () => {
    
    }
    #在组件完成更新后立刻调用dom中的数据
    componentDidUpdate = () => {
    
    }
    #在组件从dom中移除时立刻调用
    componentWillUnmount = () => {
    #componentDidMount中计时器之类的可以在这里进行解绑
    }
    render(){
        return (
            <div style={{color: this.state.color}}>
            <span id="childElement"></span>
            hello,{this.props.name}
            </div>
        )
    }
}  
ReactDOM.render(
    <ClassCompoennt name="world" />,
    document.getElementById('example')
)
```
## Props 和 State
``` bash
const ChildEle = (props) => {
    return <span>{props.color}</span>
}

class ClassCompoennt extends React.Component{
    state = { #方法
        color: 'red'
    }
    changeColor = () => { #方法且
        # this.setState({     #方法中的属性
        #   color: this.state.color === 'green' ? 'red' : 'green'
        # })
        # preSate ->  state对象
        this.setState((preState) => ({
            color: preState.color === 'green' ? 'red' : 'green'
        }))
        this.setState((preState) => {
            console.log(preState)     
            #==> 同步的与color
        })
    # console.log(this.state);  #state是一个异步的操作。
    }
    render(){
        return (
            <div 
            onClick={this.changeColor}
            style={{color: this.state.color}}
            >
            hello world
            <hr/>
            <ChildEle color={this.state.color}/>
            
            </div>
        )
    }
}
ReactDOM.render(
    <ClassCompoennt />,
    document.getElementById('example')
)
```
`props`和`state`都被定义在实例上，直接this就可以取到，但是在修改tate的时候，必须遵循他的语法，this.setState语法，其参数`preState`则就是指向的tate的对象，这里在学习小程序之后应该会有一定的理解。

## react-router 配置及使用
### 安装
```bash
npm install react-router-dom
```
### react-router  配置简单混个脸熟
* BrowserRouter路由
    BrowserRouter ==> baidu.com/a/b
* HashRouter路由
    HashRouter  ==> baidu.com/#/a/b

#### BrowserRouter路由简单使用
* inclusive router  默认尽量广义上匹配
* exclusive router
    Switch简单匹配且只返回第一个匹配的（不精准）
* exact 属性，精准匹配
* Redirect 重定向


```bash
import React, { Component } from 'react';
+ import { BrowserRouter, Route, Switch, Redirect} from 'react-router-dom';
  #先引入组件BrowserRouter、Route
import './App.css';

const Test = () => {
  return <p> 测试组件 </p>;
}
const NotFoud = () => {
    return <p>404 找不到 </p>
}
const isLogin = true;
class App extends Component {
  render() {
    return (
      <BrowserRouter>
        <Switch>
            <Route path = "/" exact component = {Test} />
            <Route path = "/test" exact component = {Test} />
            <Route path = "/user" exact render={() => isLogin ? <User/> : <Redirect to="login" />} />
            <Route component={NotFoud} />
        </Switch>        
      </BrowserRouter>
    );
  }
}
export default App;
```
下载react-router-dom之后，顶上引入两个组件，Route是配置路由组件，path是表示路由地址，component路由对应的组件是什么。达到简单的路由配置组件
