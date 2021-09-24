---
title: javascript设计模式（一）
categories: javascript
tags: JavaScript
date: 2018-05-18 09:47:00
---
# 设计模式类型

## 创建型
单例模式、 抽象工厂模式、 建造者模式、 工厂模式、 原型模式

## 结构型模式
适配器模式、 桥接模式、 装饰者模式、 组合模式、 外观模式、 享元模式、 代理模式

## 行为型模式
命令模式、迭代器模式、观察者模式、中介者模式、备忘录模式、解释器模式、状态观察模式、策略模式、责任链模式、访问者模式
# 设计模式六大原则 
减少耦合、增强复用性、降低代码的开发维护扩展成本   
* 单一原则
* 里氏代换原则
任何基类可以出现的地方，子类一定可以出现，**设计基类的时候，只当做接口，不做任何条件功能**
* 依赖倒置原则DIP
通常依赖接口，不依赖方法，底层的东西不用了解，我们只需要表现就可以了，降低耦合度
* 接口分离原则ISP
把大的接口拆分小接口，不能一个接口全部实现增删改查
* 迪米特法则
一个接口和一个方法，传入的参数越少越好，能让复杂程度降低，降低耦合度，关联越少越好'
* 开闭原则OCP
面向扩展开发，面向修改关闭

# 设计模式---单例模式
单例模式的思想。当方法被第一次创建出来之后立刻被存起来,便于之后的操作直接调用
## 单例典型之一
``` bash
var Test = (function(){
    # var this = Object.create(Test.prototype) 
    # return this;
    # 函数new实例做的隐性的操作
    var instace;
    return function(name){
        if(typeof instace === 'object'){
            #当第二次new直接进入这里 instace === this ==>  {}
            return instace;
        }
        instace = this; #this -> {}
        this.name = name;
    }
})()
var a = new Test();
Test.prototype.lastName = 'deng'
var b = new Test();
console.log(a == b, a.lastName)
```
典型的单里模式，保证闭包的开闭严重。下面还有更精彩....

## 单例模式应用

### 普通模式
在普通模式下会无限创建弹框...
``` bash
#创建弹窗
var CreateAlert = function(text){
    var oDiv = document.createElement('div');
    oDiv.style.display = 'none';
    oDiv.innerText = text;
    document.body.appendChild(oDiv);
    return oDiv;  #retrun出来用于之后的操作
}
oBtn.onclick = function(){
    var oDiv = CreateAlert('hello world');
    oDiv.style.display = 'block';
}
```
一目了然每次一次创建弹窗都是新的开始，很耗性能

### 单例模式
``` bash
var singleAlert = (function(){
    var oDiv = undefined;
    return function(text){
        if(typeof oDiv === 'object'){
            return oDiv
        }
        oDiv = document.createElement('div')
        oDiv.style.display = 'none'
        oDiv.innerText = text
        document.body.appendChild(oDiv)
        return oDiv
    }
})()
oBtn.onclick = function(){
    var oDiv = singleAlert('hello world');
    oDiv.style.display = 'block';
}
```
单个功能使用单例模式，再来个高级一点的

### 单例模式通用版,将普通模式变成单例模式
利用例一的普通模式，建立一个复合方式，转换成单例模式，你好我好大家好
``` bash
#创建弹窗
# 普通模式
var CreateAlert = function(text){
    var oDiv = document.createElement('div');
    oDiv.style.display = 'none';
    oDiv.innerText = text;
    document.body.appendChild(oDiv);
    return oDiv;  #retrun出来用于之后的操作
}
#传func 返回新的函数 复合模式 --> 单例
var getSingle = function(func){
    var result;
    return function(){
        #aruments;
        if(!result){
            result = func.apply(this, arguments);
        }
        return result;
    }
}
var singleAlert = getSingle(CreateAlert);

oBtn.onclick = function(){
    var oDiv = singleAlert('hello world');
    oDiv.style.display = 'block';
}
```
# 设计模式---代理模式
为一个对象提供一种代理以控制这个对象的访问
** 虚拟代理 **
虚拟代理是吧一些开销较大的对象，延迟到真正需要他的时候才去创建执行，例如图片懒加载
** 保护代理 **
控制真实的访问权限，例如登录操作后才能看到全功能
** 远程代理 **
一个对象不同的对象进行局部代理，例如监控多个对象状态，总机监控分店
** 智能代理 **
调用对象代理处理另外一些事情如垃圾回收机增加额外的服务，例如火车代售处

## 代理模式应用
用一个小场景，mrDeng送花给女神的场景。通过代理判断女神随机性心情，代理监控心情false的时候，只让其输出‘ok’。
``` bash
var mrDeng = {
    sendFlower: function(target){
        var flower = 'sunflower';
        target.receiveFlower(flower);
    }
}
#代理
var proxy = {
    proxyFlower: function(target){
        this.listenMood(target, function(){
            mrDeng.sendFlower(target);
        })
    },
    #检测女神心情
    listenMood: function(target, cb){
        var timer = setInterval(function(){
            if(target.mood){
                cb();
                clearInterval(timer);
            }
        }, 500)
    }
}
#女神
var goddess = {
    mood: null, # 心情true（好） & false（坏）
    #收花功能
    receiveFlower: function(flower){
        # true -> good   false -> bad
        this.mood ? console.log('ok') : console.log('get out');
    },
    changeMood: function(){
        this.mood = Math.random() > 0.5
    },
    # 心情
    createModd: function(){
    # 改变女神心情。调用方法
        var self = this;
        self.changeMood()
        setInterval(function(){
            self.changeMood();
        }, 400)
    }
}
goddess.createModd()
#代理
proxy.proxyFlower(goddess)
```
## 单一图片懒加载
``` bash
var MyImage = function(id){
    var oImg = new Image();
    this.setSrc = function(src){
        oImg.src = src;
    }
    document.getElementById(id).appendChild(oImg);
}

var ProxyImage = (function(){
    var oMyImg = new MyImage('demo');
    var oNewImage = new Image();
    oNewImage.onload = function(){
        oMyImg.setSrc(oNewImage.src);
    }
    return function(src){
        oMyImg.setSrc('./timg.jpg')
        oNewImage.src = src;
    }
})()
ProxyImage('这是图片路劲...');
```
## 实例--虚拟代理完成多功能整合处理
### html标签
```bash
<div>duyi</div>

<button type="addWidth">add width</button>
<button type="addHeight">add hieght</button>
<button type="addBg">add bg</button>
<button type="addFs">add Fs</button>
<button type="addFw">add Fw</button>
<button type="addC">add C</button>
```
### js脚本语言
```bash
var demo = document.getElementsByTagName('div')[0];
var oBtnArray = document.getElementsByTagName('button');
for (var i = 0; i < oBtnArray.length; i++) {
    oBtnArray[i].onclick = function () {
        realDeal(this);
    }
}
function deal() {
    switch (this.getAttribute('type')) {
        case 'addWidth':
            demo.style.width = demo.offsetWidth + 50 + 'px';
            break;
        case 'addHeight':
            demo.style.height = demo.offsetHeight + 50 + 'px';
            break;
        case 'addBg':
            demo.style.backgroundColor = 'orange';
            break;
        case 'addFs':
            demo.style.fontSize = 25 + 'px';
            break;
        case 'addFw':
            demo.style.fontWeight = 'bold';
            break;
        case 'addC':
            demo.style.color = 'green';
            break;
    }
}
var MyProxy = function(func){
    var cache = [];
    var timer = null;
    var arg = arguments;
    return function(){
        cache.push(arguments[0]);
        clearTimeout(timer);
        timer = setTimeout(function(){
            for(var i = 0; i < cache.length; i++){
                func.call(cache.shift());
            }
        }, 2000)
    }
}
var realDeal = MyProxy(deal)
```

# 策略模式
策略模式提供了管理相关的算法办法，策略模式提供了可以替换继承关系的办法、使用策略模式可以避免使用多重条件转移语句
## 实例--表单认证,保护代理
### html代码
```bash
<div>
    用户名：<input type="text" id="userDom" name="userName">
    <span id="showUser"></span>
    <br />
    密  码：<input type="password" id="psDom" name="password">
    <span id="showPs"></span>
    <br />
    邮  箱：<input type="text" id="emailDom" name="password">
    <span id="showEmail"></span>
    <br />
    <button id="loginDom">login</button>
</div>
```
### js代码
```bash
<script src="strategy.js"></script>
<script>
  # 规则用户名不能为空，字符不得超过5
  # 密码不能为空，密码长度不少于6
  let loginDom = document.getElementById('loginDom');
  let userDom = document.getElementById('userDom');
  let psDom = document.getElementById('psDom');
  let showUser = document.getElementById('showUser');
  let showPs = document.getElementById('showPs');
  let emailDom = document.getElementById('emailDom');
  let showEmail = document.getElementById('showEmail');
  loginDom.onclick = function () {
	  ProxyRequest('ok')
  }
  function Request () {
	  console.log('ok');
  }
  let validator = new Validator();
  validator.extend({
    type: 'isEmail',
    handler: (value, errorMsg) => {
	  if (value === '') {
		return errorMsg
	  }
	  return true
    }
  })
  # add
  # showDom 提示dom标签
  # strategies = {} 校验算法
  # extend -> 扩展接口
  let ProxyRequest = (function () {
    let checkUser = [
      {strategy: 'isNonEmpty', errorMsg: '用户不能为空'},
      {strategy: 'maxLength: 5', errorMsg: '用户名最大不能超过5'}
    ]
    let checkPs = [
	  {strategy: 'isNonEmpty', errorMsg: '密码不能为空'},
	  {strategy: 'minLength: 6', errorMsg: '密码名长度不能少于6'}
    ]
    let isEmail = [
	  {strategy: 'isEmail', errorMsg: '邮箱不能为空'},
    ]
    validator.add(userDom, showUser, checkUser)
	validator.add(psDom, showPs, checkPs)
	validator.add(emailDom, showEmail, isEmail)
    return function () {
      let arg = arguments;
      let flag = validator.start();
      console.log(flag)
      if (flag) {
		Request.apply(this, arg);
      }
    }
  })()
```
### strategy.js代码
```bash
# 校验规则函数
Validator.prototype.strategies = {
  isNonEmpty: function (value, errorMsg) {
	if (value === '') {
	  return errorMsg
	}
	return true
  },
  maxLength: function (value, length, errorMsg) {
	if (value.length > Number(length)) {
	  return errorMsg
	}
	return true
  },
  minLength: function (value, length, errorMsg) {
	if (value !== '' && value.length < Number(length)) {
	  return errorMsg
	}
	return true
  }
}
# 校验规则添加
Validator.prototype.extend = function (config) {
  this.strategies[config.type] = config.handler
}
# 添加校验的规则集合
Validator.prototype.add = function (dom, showDom, strategyArray) {
  # 校验对象， 校验提示对象， 校验规则数组对象
  # strategyArray: [
  # {strategy: 'isNonEmpty', errorMsg: '用户不能为空'},
  # {strategy: 'maxLength: 5', errorMsg: '用户名最大不能超过5'}
  #  ]
  this.showDom.push(showDom)
  strategyArray.forEach(ele => {
	this.cache.push(() => {
	  #type找到对应的校验规则
	  let typeArray = ele.strategy.split(':');
	  # shift方法从数组中返回第一个元素,此方法更改数组的长度
	  let type = typeArray.shift();
	  typeArray.unshift(dom.value)
	  typeArray.push(ele.errorMsg)
	  let msg = this.strategies[type].apply(this, typeArray)
	  if (msg != true) {
	    showDom.innerText = msg
	  }
	  return msg
	})
  })
}
# 执行校验
Validator.prototype.start = function () {
  let flag = true
  this.showDom.forEach(dom => {
    dom.innerText = ''
  })
  this.cache.forEach(func => {
    if (func() !== true) {
      flag = false
	} 
  })
  return flag
}

function Validator() {
  # 缓存
  this.cache = []
  this.showDom = []
}

```
