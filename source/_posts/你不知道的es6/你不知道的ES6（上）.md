---
title: 你不知道的ES6之上篇
categories: 你不知道的ES6 
tags: ES6
date: 2018-05-01 09:10:00
---
# 块级作用域
## let 命令
先申明在使用否则报错
* 不存在变量提升
* 暂时性死区
* 不允许重复声明

```bash
let i = 10; # 全局作用域
let arr = [];
for(let i = 0; i < 10; i++){ 
    let i = 12;
    arr[i] = i;
}
```
上面分别表示的三种不同的层级作用域。所以不存在**重复声明**，不会报错
## const
const 也是块级作用域。 不可以改变的变量是个常量；定义的是目标地址。所以不会改变。因此对象可以增添改查；
```bash
const a = 123;
a++ #会报错
const obj = {}
obj.a = 123 #不会报错
```
# 变量的结构赋值
## 数组的结构赋值
* 实质：模式匹配

```bash
let [a, b, c] = [1, 2, 3];
let [a, [b,[c]]] = [1, [2, [3]]]
# a = 1, b = 2, c = 3
let let [a, b] = [2]
# => let [a, b] = [2, undefined] 称为结构失败
let [a, b] = [1, 2, 3]  #称为不完全结构
let [x = 10] = [20]   #支持默认值，对应结构匹配是否绝对等于undefined
let [a] = 1 #会报错，右边不具备迭代器接口所以不行
let [x = 10] = [20]  #支持默认值，先看右边有没有赋值，是否绝对等于undefined，再走左边，如nul就不行

function fn (){
    console.log("xxxx")
    return 2
}
let [x = fn()] = [3]
模式匹配属于惰性赋值，不走默认值的情况下不会执行fn。
```
## 对象的结构赋值
```bash
let {bar, foo} = {foo: 234, bar: 123}; 
let {bar: bar, foo: foo} = {foo: 234, bar: 123}
#对象的结构赋值是无序的
let {foo: bar} = {foo: 123};
#foo==>undefined  
#bar ==> 123
#foo只代表模式，，不具有真正的意义

let obj = {
    p: [
        'hello',
        {y: 123}
    ]
}
let {p:[x,{y: aa}]} = obj;
# x => hello   aa => 123

let {pow, ceil, floor} = Math;
#函数的结构赋值，提取Math中的方法
#Math = {
#   pow: xxx,
#   ceil: xxx,
#   floor: xxx
#}

let {toString} = 123;
let{toString: s} = false;
console.log(toString == Number.prototype.toString);  # true
console.log(s == Boolean.prototype.toString);       #true

let arr = [[1,2], [3, 4]].map(function([a, b]){
    return a + b
})
# arr = [3, 7]
```
## swap数据交换
```bash
let a = 1, b = 2;
[a, b] = [b, a];

# 函数返回值的结构
 function methods(){
    return {
        add: function(){},
        sum: function(){}
    }
}
let {add, sum} = methods();
```

## reduce方法，消灭for循环
### 将多层数组转换成一层数组
```bash
const nestedArr = [1, 2, [3, 4, [5, 6]]];
const flatten = arr =>
  arr.reduce(
    (flat, next) => flat.concat(Array.isArray(next) ? flatten(next) : next),
    []
  );
```
### 将下面数组转成对象，key/value 对应里层数组的两个值
```bash
const objLikeArr = [["name", "Jim"], ["age", 18], ["single", true]];
const fromPairs = pairs =>
  pairs.reduce((res, pair) => ((res[pair[0]] = pair[1]), res), {});

fromPairs(objLikeArr);
# {
#   age: 18
#   name: "Jim"
#   single: true
# }
```
### 将用户中的男性和女性分别放到不同的数组里：
```bash
const users = [
  { name: "Adam", age: 30, sex: "male" },
  { name: "Helen", age: 27, sex: "female" },
  { name: "Amy", age: 25, sex: "female" },
  { name: "Anthony", age: 23, sex: "male" },
];
const partition = (arr, isValid) =>
  arr.reduce(
    ([pass, fail], elem) =>
      isValid(elem) ? [[...pass, elem], fail] : [pass, [...fail, elem]],
    [[], []],
  );
  
const isMale = person => person.sex === "male";

const [maleUser, femaleUser] = partition(users, isMale);
```
## 数组方法实例
### 1.filter将数组中的false值去除
```bash
const arrContainsEmptyVal = [3, 4, 5, 2, 3, undefined, null, 0, ""];
const compact = arr => arr.filter(Boolean); 
```
### 2.map将数组中的 VIP 用户余额加 10
```bash
 const users = [   
      { username: "Kelly", isVIP: true, balance: 20 },   
      { username: "Tom", isVIP: false, balance: 19 },   
      { username: "Stephanie", isVIP: true, balance: 30 } 
 ];
 users.map(   user =>(user.isVIP ?{...user, balance: user.balance +10}: user));
```
### 3.evert判断用户是否全部是成年人
此方法返回true放在空数组上的任何条件。
```bash
const users = [
  { name: "Jim", age: 23 },
  { name: "Lily", age: 17 },
  { name: "Will", age: 25 }
];
users.every(user => user.age >= 18);
#  找出上面用户中的第一个未成年人
const findTeen = users => users.find(user => user.age < 18);
findTeen(users);
```
### 4.Set将数组中重复项清除
```bash
const dupArr = [1, 2, 3, 3, 3, 3, 6, 7];
const uniq = arr => [...new Set(arr)];

uniq(dupArr);
```

# 字符串的扩展
## 模板字符串
* 增强的字符串
* 在字符串中添加变量（表达式）

```bash
#变量
var xm = {
    age: 18,
    height: 180,
    name: 'xiaoming'
}
var {age, nam, height} = xm;
var str = 'name:' + name + 'age:' + age + 'height:' + height  
var newStr = `name:${name} age:${age} height: ${height}`      #ES6语法
# 表达式
var x = 3,
    y = 5;
str = `${x} + ${y} = ${x + y} `;
#   =  3 + 5 = 8
function get(){
    return 'my is you fatter';
}
var str = `${get()} `  
# =>my is you fatter
# 嵌套，模板字符串嵌套模板字符串
var arr = [1, 2, 3, 4, 5];
var resStr = `${arr.map(function(item, index){
    return `${item}: ${index}`
})}`
```
## 标签模板  
* 标签模板实质————函数调用的特殊形式
* 重要应用————过滤HTML字符串

```bash
# 例1
function show(name){
    console.log(name);
}
show `xiaoming` #=> Array[1]的数组。0: 'xiaoming'
# 例2
var name = "xiaoming"
var where = "BJ";
function show(){
    console.log(arguments);
}
show `hello ${name}, welcome to ${where} `;
# arguments => [["hello ", ", welcome to", " "], "xiaoming", "BJ"]
```
### 标签模板
```bash
function print(arg1, arg2){
    console.log(arguments)
}
print`12${3}4${5}6`
# 安装模板分开  ['12', '4', '6']
# 把模板中的内容当成若干参数
function print(listArr, ...arg) {
    console.log(listArr)
    console.log(arg)
}
var a = 10
var b = 10
print`${a}*${b}=${a*b}`
# listArr => ["", "*", "=", "", raw: Array(4)]
# ...arg=> [10, 10, 100]
```
#### 解决xxs攻击
```bash
let name = '<script>console.log("你中招啦")<\/script>'
function safeHTML(arr, name){
    console.log(arr) # ["<p>你好，", "</p>", raw: Array(2)]
    console.log(name) # <script>console.log("你中招啦")</script>
    return `${arr[0]}${name.replace(/&/g, '&amp').replace(/</g,'&lt').replace(/>/g,'&gt;')}${arr[1]}`
}
document.write(safeHTML`<p>你好，${name}</p>`)
```
### 例题：过滤HTML字符串
```bash
var name = `<script>alter("aaa")</\script>`
safeHTML `<p>${name}Welcome to duyi!!!</p> `
function safeHTML(data){
    #data =>["<p>", "welcomet to duyi!!!</p>"]
    #arguments => [["<p>", "welcomet to duyi!!!</p>"], '<script>alter("aaa")</\script>']
    var str = data[0];            
    for(let i = 1; i < arguments.length; i++){
        var arg = String(arguments[i]);  
    # arg => '<script>alter("aaa")</\script>'
        str += arg.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/, '&gt;');
        console.log(str)
        str += data[i];
    }
}
```
## 字符串的方法扩充
#### repeat()  padStart()  padEnd() 方法运用
```bash
#repeat() 返回新的字符串，将原字符串重复n次
'x'.repeat(3)            # => "xxx"
'hello'.repeat(2)        # => 'hellohello';
padStart(), padEnd();
'x'.padStart(5, 'ab')    # => 'ababx'
'x'.padStart(4, 'ab')    # => 'abax'
'x'.padEnd(5, 'ab')      # => 'xabab'
'x'.padEnd(4, 'ab')      # => 'xaba'
```
### charAt(num)
返回当前字符串的第几个字符
```bash
let str = 'welcome to duyi'
str.charAt(3) # => c
```
### charCodeAt
unicode编码
```bash
let str = 'welcome to duyi'
str.charCodeAt(3) # => 99
```
### slice（start=0, end=this.length）
字符串截取[start, this.length)
```bash
let str = 'welcome to duyi'
str.slice(0, 4) #=> welc
```
### indexOf(str, start=0)
判断是否有`str`所包含的字符串，如果有就以第一个索引返回，没有返回-1
```bash
let str = 'welcome to duyi'
str.slice('com') # => 3
# 第二个为索引从第几个开始匹配
```
### split()
安装规则拆分，如：split(','),安装`,`好拆分
### join
与split方法互逆
```bash
[1,2,3,4,5].join(',') #=> '1,2,3,4,5'
str='1,2,3,4,5'
str.spilt(',') #=> ["1", "2", "3", "4", "5"]
```
### toUpperCase, toLowerCase
toUpperCase将所有无论大小变成大写字母
toLowerCase将所有变成小写字母
## 字符串方法实例
### 1.判断字符串中是否含有元音字母
```bash
const randomStr = "hdjrwqpi";
const isVowel = char => ["a", "e", "o", "i", "u"].includes(char);
const containsVowel = str => [...str].some(isVowel);

containsVowel(randomStr);
```
### includes
字符串识别，与indexOf类型es6强化的方法 返回true，false
```bash
let str = 'you were, are and will be in my heart'
str.includes('heart') # true
# 也支持第二个参数，从第几位开始
```
### startsWith(str), endsWith(str)
startsWith(str)是否从str开始
endsWith(str)是否从str结束
```bash
let str = 'you were, are and will be in my heart'
str.startsWith('you')  # true
str.endsWith('heart')  # true
# 也支持第二个参数 [)
str.startsWith('w',4)  # true
str.endsWith('you w', 5) #true
# 实例用于筛选
let stuffLists = [
    'goole-page', 
    'alibaba-mahuateng',
    'facebook-zuckerberg',
    'duyi-ding'
    'duyi-song'
    'duyi-cheng'
]
let duyistuffs = stuffLists.filter(stuff=>stuff.startsWith('duyi-'))
## 帅选出来以'duyi'为开头的数组字符串 
```
### repeat重组
将当前字符重复机制
```bash
let str = '*'.repeat(10)
# **********
```

# 函数的扩展
## 函数参数的默认值
* 基本使用
* 结构赋值结合使用
* 参数作用域以及注意事项

### 基本使用例题
```bash
function Person(name, age=18){
    console.log(name, age)
}
Person('xm', undefined);  #除非是undefined走默认值，否则赋值什么弄什么
Person('xm', 12);         # xm, 12
Person('xm', 0);          # xm, 0
Person('xm', null);       # xm, null
Person('xm', false);      # xm, false
```
### 结构赋值例题
```bash
# 例题1：
function fn({x, y=5}){
    console.log(x, y);
}
fn({});                   # undefined, 5
fn({x:1});                # 1, 5
fn({x:1, y: 2});          # 1, 2
fn();                     # 报错

# 例题2：
function f1({x=0, y=0} = {}){       #有默认值
    console.log(x, y);
}
function f2({x, y} = {x:0, y:0}){   #提前赋值
    console.log(x, y)
}
f1()                                        # => 0, 1
f2()                                        # => 0, 1
f1({x:1, y: 2});                            # => 1, 2
f2({x:1, y: 2});                            # => 1, 2
f1({x:1});                                  # => 1, 0
f2({x:1});   # ==> function f2({x, y} = {x:1})   1, undefined;
```
### 参数作用域以及注意事项
```bash
function fn(x=10){        # =>let x = 10
    let x = 2;
}
fn()                     # =>报错x已经被定义不能重复定义
```
## rest参数（扩展运算符）
```bash
function fn(...arg){  
    console.log(...arg)  
    # arg => [1, 2, 3, 4, 5]  数组
    # ...arg => 1, 2, 3, 4, 5 ...arg相当于把把[]给去掉
}
fn(1, 2, 3, 4, 5);
# 例题 
var arr = [1, 2, 3, 4, 5];
fn.call(null, ...arr)  # =>... 去掉[]
fn.apply(null, arr)
function fn(...args){
    console.log(args)
}
```
## 箭头函数
* 基本使用方法
* 注意事项
* 嵌套的箭头函数

### 基本使用方法
```bash
var fn = (num1, num2) => (num1 + num2) # 返回的参数
# 等价于
function fn(num1, num2){
    return num1 + num2
}

var f = () => ({name: 123})  # 返回的一个对象。比须加（）
#大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错。
let getTempItem = id => ({id: id, name: 'Temp'})
[1, 2, 3].map(x => x*x);
```
### 注意事项 this的指向问题
```bash
var age = "window 18"
var obj = {
    age: 18,
    getAge: () => this.age,  #==> this.age = window 18
    setAge: function(){
        console.log(this.age)  #  this.age = 18
    }
}
# 箭头函数中没有this的概念，只会一次找他的父级，并且没有arguments。
function Timer() {
    this.s1 = 0;
    this.s2 = 0;
    # 箭头函数 this指向Timer
    setInterval(() => {
        this.s1++ ,
        console.log(this)  #=> Timer
    }, 1000);
    # 普通函数 this指向window
    setInterval(function () {
        this.s2++;
        console.log(this)  #=> window
    }, 1000);
}
var timer = new Timer();
```

### 嵌套的箭头函数
```bash
function fn(str){
    return function(){
        return str.split('');
    }
}
fn('12')()
# 等同于
let fn = str => () => str.split('');
fn('123')();
```
# 数组的扩展
* 数组的扩展运算符（...）
* Array.from() Array.of()
*  数组是实例方法  copyWithin
*  数组实例的方法  fill(),entries(), keys() values()
*  数组实例的方法  includes() find() findIndex()

## 数组扩展运算符
```bash
# 功能利用push方法实现arr的数组添加，三种方式
var arr = [];
var arr1 = [1, 2, 3, 4, 5];
# 1、apply调用数组方法
Array.prototype.push.apply(arr, arr1);
# 2、直接利用push方法走参数
arr.push(1, 2, 3, 4, 5)
# 3、...扩展运算符
arr.push(...arr1);
# 4、利用cancat数组拼接的方法
arr.concat(arr1) 
var x = [1] ; var y = [2,3,4,5]
var newArray =  [x, ...y] 
# 模式匹配
var [x, ...y] = [1, 2, 3, 4, 5]
# =>x->1 ; y->2,3,4,5
# 字符串的空数组 -> 隐形的将类数组转换成字符串数组
var strArr = [...'chenkai']
# -> [c,h,e,n,k,a,i]
```

## Array.from() Array.of()
*  Array.from() 类数组转换成数组
*  Array.of()   将一组 数转换为数组

### Array.from 类数组转换成数组应用,并且支持回调
```bash
var obj = {   #类数组
    '0': 'd',
    '1': 'c',
    '2': 'd',
    '3': 'c',
    length: 4
}
var arr = Array.from(obj, function(item, index){
    return item + index;  #支持回调函数
});
#类似与map
var arr1 = Array.from(obj).map((item, index) => item + index)
var divs = document.getElementsByTagName('div');
# ES6 小应用分装typeOf
function typeOf(){
    return Array.from(arguments, item => typeof item)
}
console.log(typeOf(2, 'dsfs', false, [], undefined, null))
# ES5 一样的效果
function arrayOf(){
    return Array.prototype.slice.call(arguments);
}

console.log(arrayOf(1, 2, 3 ,4 ,5)); # -> [1, 2, 3, 4, 5]
console.log(arrayOf(1))              # -> [1]
```
### 数组的构造方法
```bash
var arr = new Array(9) 
# 数组而且只有一个参数的时候，创建长度为n的数组
var arr1 = new Array("7")
# 非数字m，创建数组[m]
var arr2 = new Array('1', 2, 3)
# 多个参数的时候，返回多个参数构成的数组
```
### Array.of() 统一返回由参数构成的数组  
将一组数转换为数组
```bash
var arr3 = Array.of(9)
var arr4 = Array.of("5")
var arr5 = Array.of('1', 2, 3)

var arr = Array.of(1, 2, 3, 4, 5);
# => [1,2,3,4,5];
#等同于
var arr1 = new Array(1, 2, 3, 4, 5);
#不过new Array写一个参数的表示特定长度length的数组都是空的，Arrgy.of写一个参数表示一位的数组
function arrayOf(){
    console.log(arguments)    #类数组
    return Array.from(arguments)   #类数组转换成数组
}
```
### 数组是实例方法  copyWithin 废话不多想看例题
```bash
# 例1
var arr = [1, 2, 3, 4, 5]
         #[3, 4, 3, 4, 5]
arr.copyWithin(0, 3)
# 例2
var arr1 = [1, 2, 3, 4, 5, 6, 7];
          #[4, 5, 3, 4, 5, 6, 7]
arr1.copyWithin(0, 3, 5))
#从第几个开始，截取[3,5)之间的数覆盖
         # [6, 2, 3, 4, 5, 6, 7]
arr1.copyWithin(0, -2, -1));  
```
### 数组实例的方法  fill(),entries(), keys() values()
```bash
# fill 方法
var arr = new Array(5)   #建立一个5位的空数组
arr.fill(7)              #自动填充7到arr中
arr.fill(7, 3, 4)        #自动在[3,4)中填充7
# entries(), keys() values() 
var arr = ['2', 2, 4, 5, 6]
for (item of arr.keys()){  #遍历数组索引
    console.log(item) # =>  0, 1, 2, 3, 4
}
for (item of arr.values()){  #遍历数组内部值
    console.log(item) # => '2', 2, 4, 5, 6
}
for ([index, item] of arr.entries()){  
    #遍历数组索引 内部值 
    console.log(index, item)
}
#单独取值
var a = arr.values();
a.next().value  #=> 2  第一个数组
```
### 数组实例的方法 includes() find()  ()
```bash
#第一个符合条件的数组成员
var arr = [2, 2, 1, 5, 4];
var num = arr.findIndex(item => item > 2)
# 从数组左往右找到大于2的第一个数的索引 -> 1
var num1 = arr.find(item => item > 2) 
# 从数组左往右找到大于2的第一个数的值  ->  3

var arr = [NaN];
var num = arr.indexOf(NaN)   
# 判断数组是否有参数的值，有返回索引，没有返回-1,不能判定出NaN
var num1 = arr.includes(NaN)  
# 判断数组是否有参数的值，Boolean输出，可以判定出NaN   
```
# 对象的扩展 
* 对象的Object.defineProperty
* 属性的简洁表示方式以及属性名表达式 
* 属性的可枚举型以及属性的遍历
* 新的API: Object.is()、Object.assign()、Object.keys()、Object.values()、Object.entries()
* 对象的扩展运算符（...）

## Object.defineProperty
**注意：**如果描述符中同时出现，value、writble,和set、get两组的话，会出现异常，切记不要同事使用
**作用：**双向数据绑定的核心方法，主要做数据劫持操作（监控属性变化），同时是后期ES6中很多语法糖底层实现的核心方法。
### 数据描述
* value 'xxx' 属性值。默认''
* writable: true 是否可写，默认值false
* configurable: true 是否可配置。默认false
* enumerable: true 是否可枚举，默认false

### 存储描述符
* set: function() {} 属性访问器 进行写操作时调用该方法
* get: function() {} 属性访问器 进行读操作时调用该方法

两种写法
```bash
var obj = {
  tempValue: 'ck',
  get name () {
    console.log(123)
    return this.tempValue
  },
  set name (newValue) {
    console.log(321)
    this.tempValue = newValue
  }
}
```
```bash
var tempValue = ''
var obj = {}
Object.defineProperty(obj, 'name', {
  configurable: true,
  enumerable: false,
  get () {
    console.log('1231')
    return tempValue
  },
  set (newValue) {
    console.log(111111)
    tempValue = newValue
  }
})
```

## 属性的简洁表示方式以及属性名表达式 
```bash
#属性的简洁表示方式
var foo = '123';
var obj = {foo}; 
# ==> obj = {foo: '123'} 属性的简洁表示方法
function fn(x, y){
    return {x, y}  # ==>{x = x, y=y}
}
# ES6 函数表达式
var fn1 = (x, y) => {x, y}
var fn1 = function(x, y){
    return {x, y}
}
# 属性名表达式 
var obj = {
    name: 'xm',
    age: 18,
    getMsg: function(){
        console.log(this.age, this.name);
    },
    get(){   # 方法简写
        console.log(this.age, this.name);
    }
}
# 实例
let birth = '2000/01/01';
const person = {
    name: '张三',
    birth,   # 等同于 birth: birth
    bello(){console.log('我的名字', this.name)} 
     # 等同于 hello : function(){....}
}
var name = 'name',
    age = 'age'
var obj = {
    [name]: 'xiaoming',
    [age]: '15'
}
```
## 属性的可枚举型以及属性的遍历
### 枚举的重要两种方法
*  for...in 
*  Object.keys

```bash
# Object.getOwnPropertyDescriptor 方法
var obj = {
    age: 18,
    name: 'xm'
}
Object.getOwnPropertyDescriptor(obj, 'age')
# 返回对象属性方法的属性
# {
#    value: 18, 
#    writable: true, 
#    enumerable: true,    => 是否可枚举
#    configurable: true
#}
```
##  新的API: Object.is()、Object.assign()、Object.keys()、Object.values()、Object.entries()
```bash
# Object.is
# 增强版的 '===' 应用
Object.is('123', '123')     # true 
'123' === '123'             # true
Object.is(+0, -0)           # false
+0 === -0                   # true
Object.is(NaN, NaN)         # true
NaN === NaN                 # true
# Object.assign() 
# 将若干个可枚举属性放在一个对象中去
var obj = {}
var obj1 = {name: 'xm'}
var obj2 = {age: 19};
var obj3 = {getName(){console.log('allalalalal')}}
var newObj = Object.assign(obj, obj1, obj2, obj3);
# obj === newObj 将obj1 obj2 obj3 可枚举的属性全部添加到obj和newObj中去了
var newObj = Object.assign(2);
# 隐示把参数转换成对象，但是undefined和null不行，将后几个可枚举性参数赋给第一个参数
# Object.keys()、Object.values()、Object.entries()
var obj = {
    name: 'xm',
    age: 18,
    getMsg(){
        return this.name + this.age
    }
}
var keys = Object.keys(obj);          #遍历属性索引名
# keys = [name, age, getMsg]
var values = Object.values(obj);      #遍历属性值
# values = ["xm", 18, f]
var entries = Object.entries(obj);    #两个都遍历
# [["name", "xm"], ["age", 18], ["getMsg", f]]
```

## 对象的扩展运算符（...）
```bash
# 合并多个对象的可枚举属性, 与Object.assign有异曲同工之处
var obj1 = {name: 'xm'}
var obj2 = {age: 19};
var obj3 = {getName(){console.log('allalalalal')}}
var newObj = {...obj1, ...obj2, ...obj3} 
# 扩展运算符与Object.assign配合将多个对象的可枚举熟悉添加到前一个对象
let merge = (target, ...sources) => Object.assign(target, ...sources);  
```
