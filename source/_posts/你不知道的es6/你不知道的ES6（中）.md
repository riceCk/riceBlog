---
title: 你不知道的ES6之中篇
categories: 你不知道的ES6 
tags: ES6
date: 2018-05-02 11:10:00
---
# ES5 预热
## 继承
* 1、继承原型
* 2、继承私有属性

### 继承原型
下面给出的方式都可以当继承的子级对象constructor指向自己
#### （1）继承原型很多种，最佳的为圣杯模式的继承
```bash
Parent.prototype.lasName = 'aaa'
function Parent(){
  this.name = 'parent';
  this.play = [1,2]
}
Child.prototype.newName = 'bbb'
function Child(){
  Parent.call(this);
  this.type = 'child';
}
function inherit (Target, Origin){
  function F(){};
  F.prototype = Origin.prototype;
  Target.prototype = new F;
  Target.prototype.constructor = Target
  Target.prototype.uber = Object.prototype;
}
inherit(Child, Parent);
var a = new Child()
Child.prototype.lasName='ccc'
```

#### （2）ES6 Object.setPrototypeOf(Target, Origin)
```bash
Target.prototype.__proto__ = Origin.prototype
## 等同于
ES6 Object.setPrototypeOf(Target.prototype, Origin.prototype)
```
#### (3) 通过Object.create创建对象的方式继承
```bash
Target.prototype = Object.create(Origin.prototype, {
    constructor: Target
})
```

### 继承私有属性
```bash
function Plane (name) {
    this.name = name || '普通飞机'
    this.blood = 100;
}

function AttackPlane (name) {
    Plane.call(this, name);
}
let attack = new AttackPlane('攻击机');
```

# class类
constructor、static、extends、super
* class基本使用
* class的继承

## class基本使用
* constructor、实例属性、实例方法
* 静态属性和静态方法

### 传统定义构造函数
```bash
function Person(name, age){
    this.name = name;
    this.age = age;
}
```
### es6 class的构造函数
```bash
class Person{
    constructor(name, age){
        this.name = name;
        this.age = age;
    }
}
#class 构造的函数必须new
let person = new Person('ck', 19); 
```
### class 原型方法和私有方法
```bash
#函数属性放在内部实现私有化，方法放在原型上实现共享
class Animal{  
    static alive () {
        return true
    }
    #constructor构造的Animal函数属性
    constructor(name, age=1){
        this.name = name;
        this.age = age;
        this.weight = 10
    }
    # eat与drink为定义在函数原型上的方法
    eat(foot=1){
        this.weight += foot
    }
    drink(water=.5){
        this.weight += water;
    }
}
var a1 = new Animal('zaizai');
console.log(a1.eat === Animal.prototype.eat)  # true 
console.log(Animal.alive())
# true  这边是函数上的私有方法并非构造实例的
```
## class继承
### exteds 继承父级属性方法
```bash
class Animal{
    constructor(name){
        this.name = name;
        this.age = 1;
        this.weight = 10;
    }
    eat(){
        this.weight ++
    }
}
class Person extends Animal{      #Person继承Animal方法
    constructor(name, sex = 'male'){
        #super做函数时调用父类的Animal函数获取 {name, age, weight}  
        super(name) # 私有属性继承
        #隐性的Animal.prototype.constructor.call(this);
        #super.eat  至访问父级上的方法 ==>Animal.prototype+
        this.sex = sex;
        #super做对象时
        console.log(super.eat)
        # => super => Animal.prototype
    }
}
var p = new Person('xm');

class A{}
var a = new A;
console.log(a.__proto__ === A.prototype); #==>ture
class B extends A{}
console.log(B.__proto__.prototype === A.prototype);  #==> true
```

## class ES7 装饰器
```bash
const oInput = document.getElementById('inp');
const oBtn = document.getElementById('btn');

class Search {
  constructor () {
    this.keyValue = ''
  }
  url = 'urlA-';
  getContent () {
    console.log('向' + this.url + '发送网络请求，数据:' + this.keyValue);
  }
}
let search = new Search();

oInput.oninput = function () {
  search.keyValue = this.value;
}
oBtn.onclick = function () {
  search.getContent();
}
```

# 数据结构
计算机中组织数据的方式
* Map是字典，加强的对象
* Set

## Map 与 object
map若干键值对的集合
```bash
# map初始化
let map = new Map([
    ['name', 'ck'], 
    ['age', 18], 
    [1, 2]
])
# map创建
let map = new Map();
map.set('1', "wxb"); #存
map.set(1, 1);
# map => {'1' => "wxb", 1 => 1}
map.get(1)  #=> 取 1
map.get('1') #=> wxb
map.has(1)  # => true 判断是否存在
map.delete(1) #删除key为1时
map.clear()  #全部删除，map为空了
# 还支持forEach方法
map.forEach((value, key, map) => {
    console.log(value);
    console.log(key);
    console.log(map)
})
```

```bash
let obj = {};
obj['1'] = "wxb";
obj[1] = 1
# obj => ｛1：1｝
```
Map对象会区分key值的字符串和数子，但当对象object的时候，"1"和1就会发生隐式类型转换

### map对象改编的数组去重
```bash
let obj = {};
let arr = [1, '1', '1', 1, obj, obj, {}];
Array.prototype.unique = function(){
    let itemIndex = new Map();
    let resArr = [];
    this.forEach(item => {
        if(!itemIndex.has(item)){
            itemIndex.set(item);
            resArr.push(item)
        }
    })
    return resArr;
}
```

### 手撕map
```bash
function myMap(arr) {
    this.init()
}
myMap.prototype.len = 8
myMap.prototype.bucket = []
myMap.prototype.init = function () {
    for(let i=0; i<this.len; i++) {
        this.bucket[i] = {next: null}
    }
}
myMap.prototype.makeHash = function (key) {
        let hash= 0
        if((typeof key) == 'string') { 
            #取后三位字符串处理
        let len = (key.length > 3) ? key.length : 3
            for(let i=len-3; i<len; i++) {
                hash += (key[i] !== undefined) ? key[i].charCodeAt() : 0
            }
        } else {
            hash = +key
        }
        return hash
}
myMap.prototype.set = function (key, value){
    let hash = this.makeHash(key)
    let list = this.bucket[hash % this.len]
    let nextNode = list
    while(nextNode.next) {
        if(nextNode.key === key) {
            nextNode.value = value
            return
        } else {
            nextNode = nextNode.next
        }
    }
    nextNode.next = {key, value, next: null}
}
myMap.prototype.get = function (key) {
    let hash = this.makeHash(key)
    let list = this.bucket[hash % this.len]
    let nextNode = list
    while(nextNode.next) {
        if(nextNode.key === key) {
            return nextNode.value
        } else {
            nextNode = nextNode.next
        }
    }
    return 
}
myMap.prototype.has = function (key) {
    let hash = this.makeHash(key)
    let list = this.bucket[hash % this.len]
    let nextNode = list
    while(nextNode.next) {
        if(nextNode.key === key) {
            return true
        } else {
            nextNode = nextNode.next
        }
    }
    return  false
}
myMap.prototype.delete = function (key) {
    let hash = this.makeHash(key)
    let list = this.bucket[hash % this.len]
    let nextNode = list
    while(nextNode.next) {
        if(nextNode.next.key === key) {
            nextNode.next = nextNode.next.next
            return true
        } else {
            nextNode = nextNode.next
        }
    }
    return false
}
myMap.prototype.clear = function () {
        this.init()
}
```
## Set
有序列表，包含相互独立且不相等的值
数组去重超级方便
```bash
let set = new Set([1, 2, 3, 4 , 5, 5, 5, 5, '1'])
#-> set=[1, 2, 3, 4 ,5]
#默认去掉重复性
set.add(6) #添加新值，如果有就不变
set.delete(6) #删除
set.forEach()
set.clear() #清空
set.has(6) # 是否存在
```
### Set用法
#### 数组去重
仅限单纯数组，包含对象的无法做到真正去重
```bash
let o = {
  name: 1
}
let arr = [1, 2, 3, 4, o, o, {name: 123}, {name: 222}, {name: 222}, 5, 6, 1, 2, 3]
let obj = {};
let oS = new Set(arr)
console.log([...oS])
```
#### 并集和交集、差集
```bash
# 并集
let arr1 = [1, 2, 3, 2, 3]
let arr2 = [3, 2, 4, 4, 5]
let oS = new Set([...arr1, ...arr2])
console.log([...oS])

# 交集
let arr1 = [1, 2, 3, 2, 3]
let arr2 = [3, 2, 4, 4, 5]
let oS1 = new Set(arr1)
let oS2 = new Set(arr2)
let newArr = [...oS1]
newArr = newArr.filter(ele => oS2.has(ele))

# 差集
let arr1 = [1, 2, 3, 2, 3]
let arr2 = [3, 2, 4 , 4, 5]
let oS1 = new Set(arr1)
let oS2 = new Set(arr2)
let newArr = [...oS1]
newArr = newArr.filter(ele => oS2.has(ele))

let newArr1 = [...oS1].filter(ele => {
  return !oS2.has(ele)
})
let newArr2 = [...oS2].filter(ele => {
  return !oS1.has(ele)
})
console.log([...newArr1, ...newArr2])
```

# 迭代器和生成器
## 迭代器（遍历器）Iterator
`Iterator`迭代器是一个对象，专门为可迭代对象设计的统一的接口，这个方法用来遍历数据
**特点**
* 每个迭代器都有`next`方法，每次执行next，返回一个结果对象{value, done}
* value每次迭代的数据，done迭代是否结束Boolean
* 每个迭代器有专用指针，开始时指向数据结构的第一个值，每次调用next方法，返回当前值，指针下移
* 每个迭代器支持for...of循环

### 迭代器思路，创建一个可以迭代数组的迭代器
```bash
let arr = [1, 2, 3]
# arr.iterator.next()  {value: 1, done: false}
# arr.iterator.next()  {value: 2, done: false}
# arr.iterator.next()  {value: 3, done: false}
# arr.iterator.next()  {value: undefined, done: true}
# 创建一个可以迭代数组的迭代器
function createIterator(arr = []) {
    let nextIndex = 0
    return {
        next() {
            if(arr.length < nextIndex + 1) {
                return {
                    value: undefined,
                    done: true
                }
            } else {
                return {
                    value: arr[nextIndex ++],
                    done: false
                }
            }
        }
    }
}
let i = createIterator(arr)
i.next()
i.next()
```

### 默认的Iterator接口
Array、Map、Set、String、arguments、NodeLists
所有的迭代器都有Symbol.iterator属性必须通过中括号形式访问[Symbol.iterator]，返回默认的迭代器
```bash
let arr = [1, 2, 3, 4]
let map = new Map([['name', 'wxb'], ['age', 18], ['hobbit', 'boy']])
let set = new Set([1, 2, 3, 4, 5])
# arrEntries.next() 迭代器对于数组返回的[index, value]
let arrEntries = arr.entries()
# mapEntries.next()  [key, value]
let mapEntries = map.entries()
# setEntries.next()  [value, value]
let setEntries = set.entries()
for(let [key, value] of mapEntries) {
    console.log(key, value)
# entries 可循环遍历每个索引和value
}
let arrkeys = arr.keys()
let mapkeys = map.keys()
let setkeys = set.keys()
for(let key of mapkeys) {
    console.log(key)
# mapkeys循环遍历索引值
}
```
* 数组中迭代器中的指针，每次指向下一个索引
* Map，Set会把临连接表转化一个单向链表，指针每次指向一个节点，指针下移
* 对象不可迭代
* 字符串可迭代，for...of支持双字符迭代

## 生成器 Generator
用于生成迭代器 
```bash
function *createIterator(arr=[]) {
    for(let i = 0; i < arr.length; i++) {
        yield arr[i]
    }
}
var i = createIterator([1, 2, 3, 4, 5, 5])
```
### 实例yield的实战用法
异步函数同步使用，完成一件事物需要open -> load -> close,按步骤完成
```bash
function onpen() {
    console.log('onpening')
    setTimeout(() => {
        console.log('onpen Refrigrator')
        process.next()
    }, 1000)
}
function load() {
    console.log('loading')
    setTimeout(() => {
        console.log('load over')
        process.next()
    }, 3000)
}
function close() {
    console.log('closeing')
    setTimeout(() => {
        console.log('close Refrigrator')
        process.next()
    }, 1000)
}
function *createIterator() {
    yield onpen()
    yield load()
    yield close()
}
var process = createIterator()
```

## 实战
### 异步迭代生成器
背景：ajax异步回调问题
```bash
function foo(x, y) {
    ajax(
        "http://some.url.1/?x=" + x + "&y=" + y,
        function(err, data) {
            if (err) {
                // 向*main()抛出一个错误
                it.throw(err);
            } else {
                // 用收到的data回复 *main()
                it.next(data)
            }
        }
    )
}
function *main() {
    try {
        var text = yield foo(11, 31)
        console.log(text)
    }
    catch (err) {
        console.log(err)
    }    
}
var it = main()
it.next();
```
**原理；**这里并不是在消息传递的意义上使用的yield，而只是将其用于流程控制实现暂定/阻塞。实际上，它还是会消息传递，但只是生成器恢复运行之后的单向消息船体
看一下foo(...)。如果Ajax请求成功，我们调用：
it.next(data)
这会响应数据恢复生成器，意味着我们暂定的yield表达式直接接收到了这个值，然后随着生成器代码继续运行，这个值被赋值给局部变量text

### 生成器 + Promise
```bash
function foo (x, y) {
    return request(
        "http: //some.url.1/?x=" + x +"&y=" + y
    )
}
function *main(){
    try {
        let text = yield foo(11, 31)
        console.log(text)
    }
    catch(err) {
        console.error(err)
    }
}
let it = main()
let p = it.next().value
p.then(function(text) {
    it.next(text)
}, function (err) {
    it.throw(err)
})
```
## run的工具方法
```bash
function run (gen) {
    let args = [].slice.call(arguments, 1), it
    it = gen.apply(this, args)
    return Promise.resolve().then(function handleNext(value) {
        let next = it.next(value)
        return (function handleResult(next) {
            if(next.done) {
                return next.value
            } else {
                return Promise.resolve(next.value).then(handleNext, function handleErr(err) {
                    return Promise.resolve(it.throw(err)).then(handleResult)
                })(next) 
            }
        })
    })
}
```
## 简单的生成器案例，可以完善懒加载
```bash
function *foo() {
    console.log("*foo() staring")
    yield 3
    yield 4
    console.log("*foo() finished")
}
function *bar () {
    yield 1
    yield 2
    yield *foo()
    yield 5
}
let it = bar()
```