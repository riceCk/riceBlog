---
title: 技术面之四JS
categories: 2022前端面试梳理
tags: 面试题
date: 2022-04-27 09:48:00
---
## JavaScript 面试题汇总

### 1、typeof、instanceof、toString的区别
三种都是JS中用于获取数据类型的方法
#### typeof
用于笔记基础的数据类型和引用，返回的有**number**、**string**、**boolean**、**null**、**undefined**、**object**
#### instanceof
用来判断一个对象是否为某个构造函数的实例
#### toString 
想要区别对象或者数组、函数是不行的，**null**和**Array**的结构也是object

```bash
Object.prototype.toString.call(2); // "[object Number]"
Object.prototype.toString.call(''); // "[object String]"
Object.prototype.toString.call(true); // "[object Boolean]"
Object.prototype.toString.call(undefined); // "[object Undefined]"
Object.prototype.toString.call(null); // "[object Null]"
Object.prototype.toString.call(Math); // "[object Math]"
Object.prototype.toString.call({}); // "[object Object]"
Object.prototype.toString.call([]); // "[object Array]"
```

### 2、什么是作用域链
作用域链分为两种
* 全局作用域：绑定window上，为全部作用域链的最顶层
* 函数作用域：函数执行的时候有一个技术栈，并创建执行环境即执行上下文对象，上下文对象中有一个大对象
* 块级作用域

### 3、闭包作用
闭包就是通过函数嵌套的方式保证函数内外作用域隔离，以函数内部变量等信息只能在内部读取，并且外部的变量不影响内部
* 可以创建私有变量，通过在外部调用闭包函数访问函数内部的变量
* 使已经运行结束的函数上下文中的变量对象继续留在内存中

#### 特性
* 函数嵌套函数
* 函数内部可以引用函数外包的参数和变量（用于链式作用域）
* 参数和变量不会被垃圾回收机制回收

### 4、let、var和const
* var：变量提升，会将变量的生命提前到代码块顶部，赋值还是在原型的位置，若在赋值前使用，就会出现展示性死去undefined
* let：声明只在当前代码块内，var的声明会被提升到全局
* const：不可重复声明

### 5、JS事件循环
#### 解答1
js事件循环的主要部分有一个栈、两个队列、一个幕后线程。
开始的时候，执行栈中会执行全部的同步代码，同步代码中如果有异步函数调用如setTimeout、http请求等，会将对应的请求放到幕后线程去处理，当幕后线程处理完异步请求，比如setTimeout的时间到了，或者http请求获得响应，并且当执行栈中全部执行完毕（为空），会触发一次**事件循环**，将执行宏任务队列中第一个任务，任务执行完毕，执行微任务队列中全部任务。微任务队列全部执行完成后，事件循环再次启动，执行下一个宏观任务，如当前任务队列为空，他会一直等待任务的到来，这叫任务循环
* 宏任务：setTimeout setInterval用户交互操作UI渲染
* 微任务：Proise，nextTick

#### 解答2
1.首先js是单线程运行的，在代码执行的时候，通过将不同函数的执行上下文压入执行栈中来保证代码的有序执行
2.在执行同步代码的时候，如果遇到了异步事件，js引擎应不会一直等待其返回结果，而是会将事件挂起，继续执行执行栈中其他任务
3.当同步事件执行完毕后，再将异步事件对应的回调加入到与当前执行栈中不同的另一个任务队列中等待执行。
4.任务队列可以分为宏任务队列和微任务队列，当当前执行栈中事件执行完毕后，js引擎首先会判断微任务队列是否有任务可以执行，如果有就将微任务对首的事件亚茹栈中执行
5.当微任务队列中的任务都执行完成后再将判断宏任务队列中的任务。

### 6、如何判断两个对象相等
1.递归遍历
2.JSON.stringify

### 7、前端性能优化
* 算法优化
* CDN
* 图片压缩
* 使用浏览器缓存
* 图片懒加载
* 页面滚动加载
* JS、CSS、HTML等文件压缩
* 开启web服务器的gzip
* 使用nginx方向代理
* 减少DOM操作

JS防抖和节流debounce Throttle
Debounce防抖：在事件最后一次触发n毫秒之内执行事件，不管事件曾被触发过多少次。
Throttle节流：事件在第一次触发后n毫秒内，不论事件是否继续触发，都执行

### 8、for...of 和 for...in的区别
* **for...in**语句以任意顺序迭代对象的可枚举属性（遍历对象key值）
  **for...of**语句遍历可迭代对象定义迭代属性（遍历数组的值，不能遍历对象）

### 9、线程和进程的区别
* 进程：一个动态过程，是一个活动的实体。简单来说，一个应用程序的运行可以看做是一个进程
* 线程：程序执行流的最小执行单位，是进程中的实际运作单位

### 10、前端路由实现方式
#### Hash路由
Hash路由通过在地址增加`#path`实现区分页面，当hash发生变化后改变对应组件
* window.addEventListener('hashchange', this.refresh, false)可以监听路由变化

#### HTML5 HistoryApi
* window.history.back();  后退
* window.history.forward() 前进
* window.history.go(-4)  退后三个页面

### 11、深拷贝和浅拷贝
#### 浅拷贝
* Object.assign()
* 扩展运算符 ...   


#### 深拷贝
* JSON.stringify()
* 手写循环递归

### 12、箭头函数特点
> 1.更加简洁的语法
> 2.箭头函数没有自己的this，arguments，super
> 3.箭头函数this只会从自己的作用域链上一层继承this

> **区别：**
> * 1.普通的函数可以匿名，可以具体名函数，但是箭头函数都是匿名函数
> * 2.箭头函数的this指向不同，在普通函数中，this总是指向调用他的对象，如果作构造函数，this指向创建的对象实例。箭头函数本身不创建this，可以说箭头函数本身没有this，他会声明捕获所在上下文的this，供自己使用
> * 每个普通函数调用后都具有一个arguments对象，用来存储实际传递的参数
> * 箭头函数不具有prototype原型对象，而普通函数有

### 13、事件循环机制
> * 宏任务：主栈执行的任务，如：定时器、事件绑定、ajax、回调函数、node中fs操作模块等
> * 微任务：promise，async/await,nextTick等微任务

> 主栈队列就是一个宏任务，每一个宏任务执行完就会执行宏任务中的微任务，直到微任务全部都执行完，才开始执行下一个宏任务。
> 主栈全局任务（宏任务）> 宏任务中的微任务 > 下个宏任务。__,所有promise（微任务）的执行循序优先级高于setTimout定时器

### 14、this的指向
> 总结起来，this的指向规律如下几条：
> * 在函数体中，指向window
> * new方法调用构造函数，构造函数this会被绑定新创建的对象上
> * call/apply/bind方法显示调用函数时，函数体之内this会被绑定到指向参数对象上
> * 通过上下文对象调用函数时，函数体内this会被绑定该对象
> * 在箭头函数中，this指向有外层（函数或者全局）作用域来决定的。

### 15、原型与原型链
* 每个实例对象都有一个`__proto__`属性，该属性指向自己的原型对象prototype
* 每一个构造函数都有一个`protoype`属性，该属性指向实例对象的原型对象
* 原型对象里的`contructor`指向构造函数本身

![原型与原型链](https://xiejie-typora.oss-cn-chengdu.aliyuncs.  /2021-08-12-081402.png)

### 16、ES6新增了那些东西
* 1、箭头函数
* 2、字符串模板
* 3、支持模块化（import、export）
* 4、类（class）
* 5、let、const关键词
* 6、一些数组、字符串等内置方法，如：array.form（对象变成数组）,array.of
* 7、一些语法，比如扩展运算符、解构、函数默认参数
* 8、源编码相关，proxy
* 9、set和map数据解构
* 10、promise

### 17、防抖和节流
函数防抖，指的是短时间内多次触发同一个事件，只执行最后一次，或者只执行最开始的一次，中间的不执行
函数节流，指连续触发事件但是在n秒中只执行一次函数，即2n秒内执行2次。节流会稀释函数的执行频率

### 18、作用域、作用域链
> 作用域
> * 全局作用域
> * 函数作用域
> * 块级作用域

> 作用域链
> 当js中使用一个变量的时候，首先js引擎会尝试在当前作用域下寻找变量，如果没有找到，再到他的上层作用域寻找，以此内推直到找到全局作用域。

### 19.new操作具体干了什么
> * 创建一个空对象
> * 由this变量引用该对象
> * 该对象继承该函数的原型（更改原型链指向）
> * 把属性和方法加入到this引用的对象中
> * 新创建的对象由this引用，最后隐式的返回this

### 20.类的继承
#### 1.组合继承
```bash
# 基类
var Person = function (name, age) {
  this.name = name;
  this.age = age;
}
Person.prototype.test = 'this is a test'
Person.prototype.testFunc = function () {
  console.log('this is a testFunc')
}

# 子类
var Student = function (name, age, gender, score) {
  Person.apply(this, [name, age]); # 盗用构造函数
  this.gender = gender;
  this.score = score;
}
Student.prototype = new Person(); # 改变Student构造函数的原型对象
Student.prototype.testStuFunc = function () {
  console.log('this is a testStuFunc')
}

# 测试
var zhangsan = new Student('张三', 18, '男', 100)
console.log(zhangsan.name); # 张三
console.log(zhangsan.age); # 18
console.log(zhangsan.gender); # 男
console.log(zhangsan.score); # 100
console.log(zhangsan.test); # this is a test
zhangsan.testFunc(); # this is a testFunc
zhangsan.testStuFunc(); # this is a testStuFunc
```

#### 2.圣杯模式
```bash
# target 是子类，origin 是基类
# target ---> Student, origin ---> Person
function inherit(target, origin) {
  function F() {}; # 没有任何多余的属性

  # origin.prototype === Person.prototype,
  # origin.prototype.constructor === Person 构造函数
  F.prototype = origin.prototype;

  # 假设 new F() 出来的对象叫做小 f
  target.prototype = new F();

  # 而f这个对象又是target对象的原对象
  # 这意味着 target.prototype.constructor === f.constructor
  # 所以 target 的 constructor 会指向 Person 构造函数

  # 我们要让子类的 constructor 重新指向自己
  # 若不修改则会发现 constructor 指向的是父类的构造函数
  target.prototype.constructor = target;
}
# 基类
var Person = function (name, age) {
    this.name = name;
    this.age = age;
}
Person.prototype.test = "this is a test";
Person.prototype.testFunc = function () {
    console.log('this is a testFunc');
}


# 子类
var Student = function (name, age, gender, score) {
    Person.apply(this, [name, age]);
    this.gender = gender;
    this.score = score;
}
inherit(Student, Person); # 使用圣杯模式实现继承
# 在子类上面添加方法
Student.prototype.testStuFunc = function () {
    console.log('this is a testStuFunc');
}

# 测试
var zhangsan = new Student("张三", 18, "男", 100);

console.log(zhangsan.name); # 张三
console.log(zhangsan.age); # 18
console.log(zhangsan.gender); # 男
console.log(zhangsan.score); # 100
console.log(zhangsan.test); # this is a test
zhangsan.testFunc(); # this is a testFunc
zhangsan.testStuFunc(); # this is a testStuFunc
```