---
title: javascript运动(一)
categories: javascript
tags: JavaScript
date: 2018-01-26 09:10:00
---

# 匀速运动

``` bash
    function starMove(dom){
            var iSpeed;                      
            clearInterval(timer);
            if(dom.offsetLeft > 300){
                iSpeed = -8;
            }else{
                iSpeed = 8;
            }
            timer = setInterval(function(){
                if(Math.abs(300 - dom.offsetLeft) < Math.abs(iSpeed)){
                    clearInterval(timer);
                    dom.style.left = '300px'
                }else{
                    dom.style.left = dom.offsetLeft + iSpeed + 'px'
                }
            }, 30)
        }
```
封装好的运动函数，通过iSpeed为速度，利用目标距离与自身位置的差值，判定物体的停止，最后手动的移到目标点

# 缓冲运动

``` bash
    function starMove(dom) {
            clearInterval(timer);
            var iSpeed;
            timer = setInterval(function () {
                iSpeed = (300 - dom.offsetLeft) / 7;
                iSpeed = iSpeed > 0 ? Math.ceil(iSpeed) : Math.floor(iSpeed);
                 #为了满足物体从左往右运动iSpeed向上取整。从右向左运动iSpeed向下取整
                if (dom.offsetLeft == null) {
                    clearInterval(timer);
                } else {
                    dom.style.left = dom.offsetLeft + iSpeed + 'px';
                }
                console.log(888)
            }, 30)
        }
```
# 物体透明度的渐变
``` bash
    oDiv.onclick = function(){
            starMove(this, 50);         #将opacity放大100倍。
        }
        function starMove(dom, target){
            clearInterval(timer);
            var iSpeet, iCur;
            timer = setInterval(function(){
                iCur = parseFloat(getStyle(dom, 'opacity')) * 100;
                iSpeet = (target - iCur) / 7;
                iSpeet = iSpeet > 0 ? Math.ceil(iSpeet) : Math.floor(iSpeet);
                if(iCur == target){
                    clearInterval(timer);
                }else{
                    dom.style.opacity = (iCur + iSpeet) / 100;
                }
                console.log(iSpeet)
            }, 30)
        }
```

# 两个物体的链式运动

```bash
    var oDiv = document.getElementsByTagName('div');
    var timer = null;
    function getStyle(elem, prop){    #封装的style函数用于获取目标样式
        if(window.getComputedStyle){
            return window.getComputedStyle(elem, null)[prop];
        }else{
            return elem.currentStyle[prop];
        }
    }
    var targetobj = {
        width: 400,
        height: 400,
        opacity: 50,
        left: 200,
        top: 200
    }
    oDiv[0].onclick = function(){
        starMove(this, targetobj, function(){
            starMove(oDiv[1], targetobj);
        });
    }
    function starMove(dom, json, callback){
        clearInterval(dom.timer);
        var iSpeet, iCur;
        dom.timer = setInterval(function(){
            var bStop = true;
            for(var attr in json){
                if(attr == 'opacity'){
                    iCur = (parseFloat(getStyle(dom, 'opacity'))) * 100;                      
                }else{
                    iCur = parseInt(getStyle(dom, attr));
                }
                iSpeet = (json[attr] - iCur) / 7;
                
                iSpeet = iSpeet > 0 ? Math.ceil(iSpeet) : Math.floor(iSpeet);
                if(attr == 'opacity'){
                    dom.style.opacity = (iCur + iSpeet) / 100;
                }else{
                    dom.style[attr] = iCur + iSpeet + 'px';
                }
                if(iCur != json[attr]){
                    bStop = false;
                }
            }
            if(bStop){
                clearInterval(dom.timer);
                typeof callback == 'function' ? callback() : '';
            }
        }, 30)
    }
```