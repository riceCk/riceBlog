---
title: javascript设计模式（二）
categories: javascript
tags: JavaScript
date: 2018-05-19 09:47:00
---
## 工厂模式
工厂模式定义创建对象的接口，但是让子类去真正实例化，也就是工厂方法将类的实例化延迟到子类

**以飞机大战的飞机种类为例剖析工厂模式、观察者模式**
### 没学模式事前我们怎么做？ 构造函数
```bash
function SmallPlane(){
    this.width = 100;
    this.height = 100;
    
    this.touch = function(){
        console.log('die')
    }
}
var oSmallPlane1 = new SmallPlane();
function SmartPlan(){}
function AttackPlan(){}
```
构建三种类型的飞机函数，但是没有进行统一化，不便于统一管理
### 简单工厂模式
```bash
function PlaneFactory(type) {
    var oNewPlane = null
    switch (type) {
        case 'SmallPlane':
            oNewPlane = new SmallPlane();
            break;
        case 'SmartPlan':
            oNewPlane = new SmartPlan();
            break;
        case 'AttackPlan':
            oNewPlane = new AttackPlan();
            break;
        case 'BossPlane':
            oNewPlane = new BossPlane();
            break;
    }
    oNewPlane.touch = function () {
        console.log('die');
    }
    return oNewPlane
}

function SmallPlane() {
    this.width = 100;
    this.height = 100;
    this.blood = 100;
    # this.touch = function () {
    #     console.log('die')
    # }
}
function SmartPlan() {
    this.width = 200;
    this.height = 200;
    this.blood = 150;
    # this.touch = function () {
    #     console.log('die')
    # }
    this.trank = function () {
        console.log('track');
    }
}
function AttackPlan() {
    this.width = 200;
    this.height = 200;
    this.blood = 150;
    # this.touch = function () {
    #     console.log('die')
    # }
    this.attack = function () {
        console.log(attack);
    }
}

function BossPlane() {
    this.width = 100;
    this.height = 200;
    this.blood = 20000;
```
可以把公共的方法提取到`PlaneFactory`函数中去，再通过`var oPlane = PlaneFactory('SmallPlane')`创建。但是违反了开闭原则
#### 工厂方法模式
不在有一个唯一的工厂类的创建产品，而是将不同的产品交给对应的工厂子类去实现，每个产品有负责生产子工厂来创造，如果添加新的产品，需要做的是添加新的子工厂和产品，而不需要修改其他的工厂代码
```bash
function PlaneFactory () {}
  # 公共接口，所有的子类所实例化的对象都可以使用该接口
  PlaneFactory.prototype.touch = function () {
    console.log('die')
  }
  # 子类工厂都定义在原型上
  PlaneFactory.prototype.SmallPlane = function () {
    this.name = 'SmallPlane'
    this.width = 100;
    this.height = 100;
    this.blood = 100;
  }
  PlaneFactory.prototype.SmartPlane = function () {
	this.name = 'SmartPlane'
	this.width = 50;
	this.height = 50;
	this.blood = 100;
	this.track = function () {
	  console.log('track')
    }
  }
  PlaneFactory.prototype.AttackPlane = function () {
	this.name = 'AttackPlane'
	this.width = 50;
	this.height = 50;
	this.blood = 100;
	this.attack = function () {
	  console.log('attack')
	}
  }
  PlaneFactory.prototype.BossPlane = function () {
	this.name = 'BossPlane'
	this.width = 300;
	this.height = 300;
	this.blood = 1000;
	this.boomb = function () {
	  console.log('boomb')
    }
  }
  PlaneFactory.create = function (type) {
    # type => 子工厂 看一下子工厂是否和PlaneFactory 具备继承关系
    if (PlaneFactory.prototype[type] === undefined) {
      throw 'no this constructor'
    }
    if (PlaneFactory.prototype[type].prototype.__proto__ !== PlaneFactory.prototype) {
      PlaneFactory.prototype[type].prototype = new PlaneFactory()
    }
    return oNewPlane = new PlaneFactory.prototype[type]()
  }
  var oSp1 = PlaneFactory.create('SmallPlane')
  var oSp2 = PlaneFactory.create('SmallPlane')
  var oAp = PlaneFactory.create('AttackPlane')
  var oSrp1 = PlaneFactory.create('SmartPlane')
  var oSrp2 = PlaneFactory.create('SmartPlane')
  var oBp = PlaneFactory.create('BossPlane')
  PlaneFactory.prototype.fly = function () {
    console.log('fly')
  }
```
### 工厂模式 + 装饰者模式 都是通过原型实现
**装饰者模式：** 在不改变原对象的基础上，通过对其进行包装扩展（添加属性方法）
**实现** 构造函数需要一个装饰资源池，提供相应的装饰方法，提供添加装饰方法的方法
#### 工厂模式通过将方法类型定义在函数原型上，再将方法、类型的原型重新指向函数原型如此思路
```bash
#建立模式函数
function PlaneFactory(){
    #存储装饰方法的作用
    this.decorator_list = [];
}
# 装饰者模式
PlaneFactory.prototype.decorators = {
    eatOneLife : function(oP){
        oP.blood += 100;
    },
    eatTwoLife : function(oP){
        oP.blood += 200;
    },
    eatShrinkLife: function(oP){
        oP.blood -= 550;
    }
}
#装饰服
PlaneFactory.prototype.decorate = function(decorate){
    #装饰方法存储起来
    this.decorator_list.push(decorate);
}
#执行装饰者方法
PlaneFactory.prototype.done = function(){
    for(var i= 0; i< this.decorator_list.length; i++){
        #先判断`this.decorator_list[i]`传到`this.decorators`方法是否存在，存在就执行
        this.decorators[this.decorator_list[i]] && this.decorators[this.decorator_list[i]](this);
    }
    
}
#清空装饰者方法
PlaneFactory.prototype.empty = function(){
    this.decorator_list = [];
}

PlaneFactory.prototype.blood = 100;        
#工厂模式
#公共接口  所有子类所实例化的对象都可以使用该接口
PlaneFactory.prototype.touch = function(){
    console.log('die')
}
#四个子类工厂都定义在原型上 
PlaneFactory.prototype.SmallPlane = function(){
    this.name= 'SmallPlane',
    this.width = 100;
    this.height = 100;
    
}
PlaneFactory.prototype.SmartPlan = function(){
    this.name= 'SmartPlan',
    this.width = 50;
    this.height = 50;            
    this.track = function () {
        console.log(track);
    }
}
PlaneFactory.prototype.AttackPlan = function(){
    this.name= 'AttackPlan',
    this.width = 50;
    this.height = 50;            
    this.attack = function () {
        console.log(attack);
    }
}
PlaneFactory.prototype.BossPlane = function(){
    this.name= 'BossPlane',
    this.width = 300;
    this.height = 300;            
    this.boomb = function () {
        console.log('boomb');
    }
}
#创建create方法
PlaneFactory.create = function(type){
    #type => 子工厂 是否和PlaneFactory具备继承关系
    if(PlaneFactory.prototype[type] == undefined){
        throw 'no this constructor';
    }
    if(PlaneFactory.prototype[type].prototype.__proto__ !== PlaneFactory.prototype){
        PlaneFactory.prototype[type].prototype = new PlaneFactory();
    }
    # var arg = [].slice.call(arguments, 1);
    var oNewPlane = new PlaneFactory.prototype[type]();
    return oNewPlane;
}
var oSp1 = PlaneFactory.create("SmallPlane", 100, 200);
var oSp2 = PlaneFactory.create("SmallPlane");
var oAp = PlaneFactory.create("AttackPlan");
var oSrp1 = PlaneFactory.create("SmartPlan");        
var oSrp2 = PlaneFactory.create("SmartPlan");
var oBp = PlaneFactory.create('BossPlane');        
# 原始定义之后再添加方法
PlaneFactory.prototype.fly = function(){
    console.log('fly')
}
    
oAp.decorate('eatOneLife'); #添加修饰
oAp.decorate('eatTwoLife')
oAp.done();                  #执行修饰
console.log(oAp.blood)
oAp.empty();
oAp.decorate('eatShrinkLife')
oAp.done();
console.log(oAp.blood)
```
### 观察者模式（发布订阅模式）
定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都将得到通知，实际上，只要你曾经在dom节点上绑定过事件函数，那么你就曾经使用过观察者模式
```bash
function Event(){
    this.cache = {}
}
Event.prototype.on = function(type, handle){
    if(!this.cache[type]){
        this.cache[type] = [handle];
    }else{
        this.cache[type].push(handle);
    }
}   

Event.prototype.emmit = function(){
    #type
    var type = arguments[0];
    var arg = [].slice.call(arguments, 1);
    for(var i = 0; i< this.cache[type].length; i++){
        #依次执行die中的函数
        this.cache[type][i].apply(this, arg || []);
    }
}
#清空方法
Event.prototype.empty = function(type, hanlde){
    #es5的数组方法
    this.cache[type] = this.cache[type].fliter(function(ele,index){
        return ele != hanlde;
    })
} 
Event.prototype.once = function(type, hanlde){

}
var oE = new Event();
oE.on('die', function deal1(time){
    console.log('over'), time;
});
oE.on('die', function deal2(time){
    console.log('I will Be Back', time);
})
oE.emmit('die', '22:06');
```
在`Event`中创建**cache**对象，在**Event**原型上创建`on`的方法来添加到`cache`对象中去，在通过`emmit`循环遍历执行各个方法，`empty`用来清空，之后的只要执行`emmit`方法，就能更新`on`中的方法
