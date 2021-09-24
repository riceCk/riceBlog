---
title: 玩转vue全家桶，从入门到源码（续篇）
categories: Vue
tags: vue
date: 2018-07-19 11:47:00
---
# vue配置文件流向
* pcakage.json => dev / srcipt: dev => npm run dev => vue.js
* scripts/config.js : web-full-dev => web/entry-runtime-with-compiler.js
* src/platorms/web/entry-runtime-with-compiler.js 入口文件
* ntry-runtime-with-compiler.js : exprot Vue => ./runtime/index
* ./runtime/index : exprot Vue => core/index
* core/index : exprot Vue => ./init
* instace/index : vue  核心引入

# vue本体在src/core/instance/index中
```bash
function Vue(options){
    this._init(options)
}
```
# Vue相关属性、方法
```bash
Vue.prototpe = {
    _init(){},
    $data,
    $props,
    $set,
    $delete,
    $watch,
    $on,
    $once,
    $off,
    $emit,
    _update(){},
    $forecUpdata(){},
    $destroy(){},
    $nextTick(){},
    _render(){},
    __patch__(){},
    $mount(){}
}
vue = {
    dconfig,
    set,
    delete,
    nexTick,
    use,
    mixin,
    extend,
    compontent,
    directive,
    filter,
    initAssetRegisters,
    options = {
        components: {
            keepAlive,
            Transition,
            TransitionGroup
        },
        directives: {
            filter:{},
            show
        },
        filters: {},
        _base: Vue
    }
}
new vm = Vue()

Vue.$options = mergeOptions(
resolveConstructorOptions(vm.constructor),  # vue.options
options || {},                              # {val: 123}
vm)                                         # Vue
Vue.$options = {
    filters,
    components,
    directives,
    el: "#app",
    data: {val: 123}
}
```