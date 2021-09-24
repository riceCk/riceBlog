---
title: React项目详解
categories: React
tags: react
date: 2018-07-29 11:48:00
---

# CSS BEM 书写规范
## 块（block）,元素（element）,修饰符（modifier）

* -   中划线 ：仅作为连字符使用，表示某个块或者某个子元素的多单词之间的连接记号。
* __  双下划线：双下划线用来连接块和块的子元素
* _   单下划线：单下划线用来描述一个块或者块的子元素的一种状态

type-block__element_modifier
```bash
<div className="topBar">
    <div className="topBar__city">杭州</div>
    <div className="topBar__search">搜索</div>
    <div className="topBar__scan--red topBar__scan--blue">二维码</div>
</div>
#scss
.topBar{
    display: flex;
     &__city{

     }
     &__search
}
```
# react-slick组件使用
## 组件的安装
```bash
npm install react-slick --save
```
## 引入样式
### 1、外面引入（或登录网址拷贝到本地）
```bash
<link rel="stylesheet" type="text/css" charset="UTF-8" href="https://cdnjs.cloudflare.com/ajax/libs/slick-carousel/1.6.0/slick.min.css" />
<link rel="stylesheet" type="text/css" href="https://cdnjs.cloudflare.com/ajax/libs/slick-carousel/1.6.0/slick-theme.min.css" />
```
### 2、npm安装
```bash
npm install slick-carousel
@import "~slick-carousel/slick/slick.css";
@import "~slick-carousel/slick/slick-theme.css";
```

## 实例
```bash
import Slider from 'react-slick'

const PosterSlide = () => {
    const settings = {
        dots: true,   # 点点
        autoplay: true, #自动播放
        className: 'posterSlide' #给定最外层的class
    };
    return (
        <Slider {...settings}>
            <div>
                <img src="/source/slide/slide1.jpeg" alt=""/>
            </div>
            <div>
                <img src="/source/slide/slide2.jpeg" alt=""/>
            </div>
            <div>
                <img src="/source/slide/slide3.jpeg" alt=""/>
            </div>
            <div>
                <img src="/source/slide/slide4.jpeg" alt=""/>
            </div>
            <div>
                <img src="/source/slide/slide5.jpeg" alt=""/>
            </div>
        </Slider>
    );
};

```

# fetch数据获取简单的方法封装
## 创建一个request.js文件
```bash
export default async function(path, opt){
  const url = '' + path;
  const options = Object.assign({
    method: 'GET',
  }, opt);

  try{
    const response = await fetch(url, options) 
    const {data, status} = await response.json();

    if(status === 0){
      return data;
    }else{
      //错误处理
    }
  } catch(e){
    // 错误处理
    // 提示/弹窗
  }

}
```
## 其他组件中获取引用
```bash
import request from '../../helpers/request';

getData = async () =>{
    const data = await request('/index')
    console.log(data);
}
```
