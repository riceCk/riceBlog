---
title: 解读jQuery核心源码
categories: jQuery
tags: jquery
date: 2018-01-29 09:10:00
---

# jQuery 封装模式
```bash
(function(){

})(window, function(window){
    window.jQuery = Window.$ = jQuery;
    function jQuery(str){
        return new jQuery.prototype.init(str)
    }
    #init只是一个功能
    jQuery.prototype.init = function(str){
        this[0] = document.getElementById(str);
        this.length = 1;
        return this;
    }
    jQuery.prototype.css = function(){
        console.log('css');
        return this; #实现链式调用，放回的值到jquery上
    }
    jQuery.prototype.html = function(){
        console.log('html');
        return this;
    }
    jQuery.prototype.init.prototype = jQuery.prototype;
    #让init的原型回到jQuery上。
})
```
jquery为了实现无new操作，简历一个init方法，通过jquery实现new的操作，然后为了实现链式调用，定义在原型上的方法再一次返回到jquery上。并且让init的原型本来指向的object上的，强制指向jquery上面

# $('') 参数选择器思想
## 参数分为5种类型
```bash
1.string   /^  (?:  \s*(<[\w\W]+>)[^>]*  |  #([\w-]+))  $/
    $('div') $('.class')  $('ul li')  # math = null
    $('li', 'ul')                   # math = null 
    $('#id')                   #msth = ['#id',    undefine d    id)]
    $('<li>dsfs')              #math = ['  <li> lisdasdg','<li>',  undefined]
    $('<a>') $('<li></li>')    # match = [null,    '<a>',    null]
2.function
    $(function(){})
3.dom
    $(dom)
4.空
    $(null/false/undefined)
5.对象/数组/$()
    $($())
```
## 源码详解
### 整体源码
```bash
var rquickExpr = /^(?:\s*(<[\w\W]+>)[^>]*|#([\w-]+))$/,
    rsingleTag = ( /^<([a-z][^\/\0>:\x20\t\r\n\f]*)[\x20\t\r\n\f]*\/?>(?:<\/\1>|)$/i );

                                    # $(selector, context, root);
init = jQuery.prototype.init = function(selector, context, root){
    var match, elem;
    if(!selector){
    #判定 $(""), $(null), $(undefined), $(false) 然后返回参数
        return this;
    }
    root = root || rootjQuery;
    if(typeof selector === 'string'){
        if(selector[0] === "<" && selector[selector.length - 1] === ">" &&selector.length >= 3){
            match = [null, selector, null];
        }else{
            match = rquickExpr.exec(selector)
        }
        #$('#div', {html: 123})
        if(match && (match[1] || !context)){  #match 有值 context没值
            if(match[1]) {  #match有值 context有无值待定
                context = context instanceof jquery ? context[0] : context;
                #判定上下文context是jquery方法还是属性
                jQuery.merge( this, jQuery.parseHTML(  #merge合并后面的参数到this中
                match[ 1 ],
                context && context.nodeType ? context.ownerDocument || context : document,
                true
            ) );
            #jQuer.paeseHTML('<li/><span><srcipt>var a = 123</\srcipt>', document, ture);
            #=> [li, span, srcipt] 创建一个标签数组 创建dom元素
            #最后一个参数为true时运行添加脚本语言sctipt;第二个参数最后都是document；            
            #判断match正不正常符不符合一个合理的规则 &&  context 是不是一个纯对象通过{}或new object创建的
            #$('<li>dsfs')  或  $('<a>') $('<li></li>')
            if(rsingleTag.test(match[1]) && jQuery.isPlainObject( context )){
                #例如：$('div', {html:123, ds:"dt"})
                for(match in context){  #macth重新利用。相当于prop
                    if(jQuery.isFunction(this[match])){  #判定jQuery中有没有此方法
                        this[match](context[match])
                                #html(123)
                    }else{
                        this.attr(match, context[match])
                        # div.attr(ds, "dt");
                    }
                }
            }                
            # macth['#id', undefined ,id]   $('#div');
            }else{  
                elem = document.getElementById(match[2]);
                if(elem){
                    this[0] = elem;
                    this.length = 1;
                }
                return this;
            }
        #$('div') $('.class') $('div:eq(2)')
        }else if(!context || context.jquery){   
        #如果空值从jquery(document)找，有值从上下文找 || jQuery(document).find('div')
            return (context || root).find(selector)        
        #·$('ul li') 
        }else{    
            return this.constructor( context ).find( selector );
            #$('ul').find('li');
        }
    }else if(selector.nodeType){   #dom
        this[0] = selector;
        this.length = 1;
        return this;
    }else if(isFunction(selector)){ #function(){}
        return root.ready != undefined ? root.ready(selector) : 
        selector()
    }
    return jQuery.makeArray(selector, this)  #转换成类数组
```
### 拆分源码
先分成四类，function、空null等、dom、字符string
```bash
init = jQuery.prototype.init = function(selector, context, root){
    var match, elem;
     #判定 $(""), $(null), $(undefined), $(false) 如果是直接返回参数
    if(!selector){
        return this;
    }
    root = root || rootjQuery;
    if(typeof selector === 'string'){
        #下面详细讲
    }else if(selector.nodeType){
    #判定是不是dom，如果是直接赋值给this[0]
        this[0] = selector;
        this.length = 1;
        return this;
    }else if(isFunction(selector)){ 
    #判定是不是function()是就执行
        return root.ready != undefined ? root.ready(selector) : 
        selector()
    }
return jQuery.makeArray(selector, this)  #转换成类数组
rootjQuery = jQuery(document)
}
```
详细简介string类型
```bash
$('div') $('.class')  $('ul li')  # math = null
$('li', 'ul')                   # math = null 
$('#id')                   #msth = ['#id',    undefine d    id)]
$('<li>dsfs')              #math = ['  <li> lisdasdg','<li>',  undefined]
$('<a>') $('<li></li>')    # match = [null,    '<a>',    null]
```
string有五种类型，下面的分别提出match有值的情况下

```bash
if(selector[0] === "<" && selector[selector.length - 1] === ">" &&selector.length >= 3){
    match = [null, selector, null];
}else{
    match = rquickExpr.exec(selector)
}
```
当match有值的情况下
```bash
#判定match[1] 有值 或者 context没值
if(match && (match[1] || !context)){
    #判断match有值的情况
    if(match[1]){
        #match有值 context有无值待定 是创建标签类型的
        context = context instanceof jquery ? context[0] : context;
        #判定上下文context是jquery方法还是属性
        
        jQuery.merge( this, jQuery.parseHTML(  #merge合并后面的参数到this中
        match[ 1 ],
        context && context.nodeType ? context.ownerDocument || context : document,
        true
        ) );
        #jQuer.paeseHTML('<li/><span><srcipt>var a = 123</\srcipt>', document, ture);
        #=> [li, span, srcipt] 创建一个标签数组 创建dom元素
        #最后一个参数为true时运行添加脚本语言sctipt;第二个参数最后都是document；

        #然后开始处理context有的情况下，
        #先判定match正不正常符不符合一个合理的规则  &&  context 是不是一个纯对象通过{}或new object创建的
        #$('<li>dsfs')  或  $('<a>') $('<li></li>')
        if(rsingleTag.test(match[1]) && jQuery.isPlainObject( context )){
            #例如：$('div', {html:123, ds:"dt"})
            for(match in context){  #macth重新利用。相当于prop
                if(jQuery.isFunction(this[match])){  #判定jQuery中有没有此方法
                    this[match](context[match])
                            #html(123)
                }else{
                    this.attr(match, context[match])
                    # div.attr(ds, "dt");
                }
            }
        }
    }else{
        #判定match没值 从而肯定为$('#div') 类型的
        elem = document.getElementById(match[2]);
        if(elem){
            this[0] = elem;
            this.length = 1;
        }
        return this;
    }
}
```
当match没值的情况
```bash
$('div') $('.class')  $('ul li')  # math = null
$('li', 'ul')                   # math = null 

if(!context || context.jquery){   
#如果空值从jquery(document)找，有值从上下文找 || jQuery(document).find('div')
    return (context || root).find(selector)
}else{    #$('ul li') 
    return this.constructor( context ).find( selector );
    #$('ul').find('li');
}
```
# extend()函数分析
```bash
jQuery.extend = jQuery.fn.extend = function(){
    var src, copyIsArray, copy, name, options, clone,
        target = arguments[0] || {},
    #常见的用法jQuery.extend(obj1, obj2)，此时target为arguments[0];
        i = 1,
        length = arguments.length,
        deep = false;
    if(type target === 'boolean'){
        #如果第一个参数为true，即jQuery.extend(true, obj1,obj2);
        deep = target;
        target = arguments[1] || {};
        i = 2;
    }
    if(typeof target !== "object" && !jQuery.isFunction(target)){
        #处理奇怪的情况，比如jQuery.extend('hell', {nick:'casper})
        target = {}
    }
    if(length === i){
        #处理jQuery.extend(obj)
        target = this;
        #jQuery.extend时，this指的是jQuery;
        --i;
    }
    for(; i < length; i++){
        if((options = arguments[i]) != null) {
            #比如说jQuery.extend(obj1, obj2, obj3)
            #options则是obj2,obj3...
            for(name in options){
                src = target[name]; #索引属性
                copy = options[name]; # 参数
                if(target === copy){
                    continue;
                }
            }
            #深拷贝，被拷贝的属性值本身对象
            if(deep && copy 
                &&(jQuery.isPlainObject(copy) || (copyIsArray = jQuery.isArray(copy))
            )){
                if(copyIsArray){
                    #被拷贝的属性值是个数组
                    copyIsArray = false;
                    clone = src && jQuery.isArray(src) ? src : [];
                }else{
                    #被拷贝的属性值是个plainObject，比如{ nick: 'casper' }
                    clone = src && jQuery.isPlainObject(src) ? src : {};
                }
                target[name] = jQuery.extend( deep, clone, copy );
            } else if (copy !== undefined){
                #浅拷贝，且属性值不为undefined
                target[name] = copy;
            }
        }
    }

}
```