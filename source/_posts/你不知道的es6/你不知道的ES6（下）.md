---
title: 你不知道的ES6之下篇
categories: 你不知道的ES6 
tags: ES6
date: 2018-05-04 09:10:00
---

# proxy&Reflect
植入代理模式的思想，以简洁易懂的方式控制外部对象的访问
## 与Object.defineProperty监听参数一个意思
```bash
let oData = {
    val: 'duyi'
}
let oProxyData = new Proxy(oData, {
    set (target, key, value, receiver) {
        console.log(target, key, value, receiver)
        Reflect.set(target, key, value) 
        # 重新赋值target对象key属性的值为value
    },
    get (target, key, receiver) {
        console.log(target, key, receiver)
        return Reflect.get(target, key) 
        # 获取target中的key属性值
    },
    has (target, key) { # 是否存在
        console.log(target, key)
        return key.indexOf('_') != -1 ? false : Reflect.has(target, key)
    },
    deleteProperty () {
    
    }
});
```

# promise对象
## Promise 为什么存在
**防止同步回调函数造成的信任问题：**   
* 调用回调过早
* 调用回调过晚
* 调用回调次数过少或者过多
* 未能传递所需要的环境和参数
* 吞掉可能出现的错误或者异常

```bash
# promise 开胃菜函数
const promise = new Promise(function(resolved, rejected){
    var num = Math.random()*100;
    if(num<80){
        resolved(num)    # => 状态是resolved
    }else{
        rejected('太大了');  # => 状态是 rejected
    }
})
#配合Promise.prototype.then()方法
promise.then(data => console.log(data + 'data'), err => console.log(err + 'err'))
```
## 异步加载图片实例
```bash
# 1/
# ES5
const loadUrl = function(url){
    new Promise(function(resolved, rejected){
    })
}
# ES6
const loadUrl = url => new Promise((resolved, rejected) => {
    let img = new Image()
    img.onload = () => resolved(img)
    img.onerror = () => rejected(new Error('can nort load image at' + url));
    img.src = url;
})
loadUrl('XXX.png').then(null, err => console.log(err))
# 2/
const myAxjax = (type='Get', url) => new Promise((resolved, rejected) => {
     $.ajax({
        type,
        url,
        success: data => resolved(data),
        error: err => rejected(err)
    })     
})
var mAjax = myAjax('xxx')
loadUrl('XXX.png').then(null, err => console.log(err))
```
## Promise.all Promise.race方法
```bash
var p1 = new Promise((resolved, rejected) => {
    resolved(123)
})
var p2 = new Promise((resolved, rejected) => {
    resolved(123)
})
var p3 = new Promise((resolved, rejected) => {
    resolved(123)
})
console.log(Promise.all([p1, p2, p3]));  
#所有的状态都是resolved，Promise.all结果就是resolved。只要有一个rejected，最后状态就是rejected
console.log(Promise.race([p1, p2, p3])) 
#不论所有状态，，只输出第一个执行的状态
```

# 探究Promise源码，手动封装！！！
**Promise(fn) 构造函数     pending => resolved/rejected**
## 巩固应用
```bash
 var p = new Promise(function(resolved, rejected){
    var num = Math.random() * 100;
    if(num > 60){
        resolved(num);      # penging => resolved
    }else{
        rejected('不及格');  # penging => rejected
    }
})
p
    .then(data => new Promise(function(resolver, rejected){
    rejected(data + 'rejected');  
}), err => err)
    .then(data => console.log(data+ 'sec'), err => console.log(err + 'err'))
```
* Promise 对象支持 **链式调用**， 在then的方法中，会返回一个新的Promise对象
* .then方法中的两个回调函数无论执行哪个。都会返回一个新的Prmise对象
* 返回值如果是Promise对象，那么接下来链式调用中的then的状态由新的返回的Primise的返回状态决定
* 返回值不是Promise对象，默认会转变成Promise对象同时对象的状态为Resolved；

## 手动封装一个Promise函数
```bash
function myPromise(fn) {
    if (typeof fn != 'function') {
        throw Error(`Primise resolver ${fn} is not a function`)
    }
    var self = this;
    this.status = 'pending';
    this.data = null;
    this.resolvedArr = [];
    this.rejectedArr = [];
    function resolved(data) {
        setTimeout(function(){
            if (self.status = 'pending') {
                self.status = 'resolved'
                self.data = data;
                self.resolvedArr.forEach(fn => fn());
            }
        }, 0)
    }
    function rejected(err) {
        setTimeout(function(){
            if (self.status = 'pending') {
                self.status = 'rejected'
                self.data = err;
                self.rejectedArr.forEach(fn => fn());
            }
        }, 0)
    }
    fn(resolved, rejected)
}
# then
myPromise.prototype.then = function(onResolved, onRejected){
    var self = this;
    if(this.status == 'resolved'){
        return new myPromise(function(resolved, rejected){
            var res = onResolved(self.data);
            if(res instanceof myPromise){
                res.then(resolved, rejected)
            }else{
                resolved(res);
            }                    
        })
    }
    if(this.status == 'rejected'){
        return new myPromise(function(resolved, rejected){
            var res = onRejected(self.data);
            if(res instanceof myPromise){
                res.then(resolved, rejected)
            }else{
                rejected(res);
            }  
        })
    }
    if(this.status == 'pending'){
        return new myPromise(function(resolved, rejected){
            self.resolvedArr.push((function(onResolved){
                return function(){
                    var res = onResolved(self.data);
                    if(res instanceof myPromise){
                        res.then(resolved, rejected)
                    }else{
                        resolved(res);
                    }  
                }
            })(onResolved))
            self.rejectedArr.push((function(onRejected){
                return function(){
                    var res = onRejected(self.data);
                    if(res instanceof myPromise){
                        res.then(resolved, rejected)
                    }else{
                        rejected(res);
                    }  
                }
            })(onResolved))
        })
    }
}
# 调用
var p2 = new myPromise((resolved, rejected) => {
    console.log(1)
    setTimeout(function(){
        resolved(2)
        console.log(3);
    }, 1000); 
})
p2.then(data=>console.log(data));

    var p1 = new Promise((resolved, rejected) => {
        console.log(1)
    setTimeout(function(){
        resolved(2)
        console.log(3);
    }, 1000);
})
p1.then(data => console.log(data))
```
## fetch结合Promise的异步请求
```bash
fetch({type, url}).then(function(){}, function(){})
```
## 阿里编程题
```bash
#实现mergePromise函数，把传进去的数组顺序先后执行，
#并且把返回的数据先后放到数组data中
 
const timeout = ms => new Promise((resolve, reject) => {
	setTimeout(() => {
		resolve();
	}, ms);
});
 
const ajax1 = () => timeout(2000).then(() => {
	console.log('1');
	return 1;
});
 
const ajax2 = () => timeout(1000).then(() => {
	console.log('2');
	return 2;
});
 
const ajax3 = () => timeout(2000).then(() => {
	console.log('3');
	return 3;
});
 
const mergePromise = ajaxArray => {
	# 在这里实现你的代码
	var data = [];
	var sequence = Promise.resolve();
	ajaxArray.forEach(function(item){
		sequence = sequence.then(item).then(function(res){
			 data.push(res);
			 return data;
		});	
	})
	return sequence;
};
 
mergePromise([ajax1, ajax2, ajax3]).then(data => {
	console.log('done');
	console.log(data); // data 为 [1, 2, 3]
});
 
#分别输出
1
2
3
done
[1, 2, 3] 
```