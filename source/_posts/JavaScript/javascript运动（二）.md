---
title: javascript运动(一)
tags: JavaScript
categories: javascript
date: 2018-01-27 09:10:00
---

# 弹力运动

## 弹力运动原理

``` bash
    function sterMove(dom){
        clearInterval(dom.timer);
        var iSpeed = 20;
        var a;
        var u = 0.9;
        dom.timer = setInterval(function(){
            a = (300 - dom.offsetLeft) / 7;
            iSpeed = iSpeed + a;
            iSpeed = iSpeed * u;
            if(Math.abs(iSpeed) < 1 && Math.abs(300 - dom.offsetLeft) < 1){
                #iSpeed到零界点的时候在-1到1徘徊，且物体到达目标点附近的时候
                #判断物体停下
                clearInterval(dom.timer);
            }else{
                dom.style.left = dom.offsetLeft + iSpeed + 'px';
            }
        },30)
    }
```
## 弹力运动实例 -- 导航栏

``` bash
    <body>
        <ul>
            <li class="nav">111</li>
            <li class="nav">222</li>
            <li class="nav">333</li>
            <li class="nav">444</li>
            <li class="bg"></li>  
        </ul>
        <script>
            var oLiArray = Array.prototype.slice.call(document.getElementsByClassName('nav'), 0)
            # 将 四个 li 组成一个数组。方便下面循环遍历添加事件;
            var oBg = document.getElementsByClassName('bg')[0];
            var timer = null;
            oLiArray.forEach(function(ele,index){
                ele.onmouseenter = function(){
                    var newLeft = this.offsetLeft;
                    sterMove(oBg, newLeft)
                }
            })            
            function sterMove(dom, target){
                clearInterval(dom.timer);
                var iSpeed = 20;
                var a;
                var u = 0.9;
                dom.timer = setInterval(function(){
                    a = (target - dom.offsetLeft) / 7;
                    iSpeed = iSpeed + a;
                    iSpeed = iSpeed * u;
                    if(Math.abs(iSpeed) < 1 && Math.abs(target - dom.offsetLeft) < 1){
                        #iSpeed到零界点的时候在-1到1徘徊，且物体到达目标点附近的时候
                        #判断物体停下
                        clearInterval(dom.timer);
                    }else{
                        dom.style.left = dom.offsetLeft + iSpeed + 'px';
                    }
                },30)
            }
        </script>
    </body>
```

# 弹力实例
``` bash
<script>
	var oDiv = document.getElementsByTagName('div')[0];
	var timer = null;
	# oDiv.onmouseenter = function(){
	# 	startMove(this);
	# }
	var lastX = oDiv.offsetLeft;
	var lastY = oDiv.offsetTop;
	oDiv.onmousedown = function(e){ # 小球拖拽运动
		clearInterval(timer);    
		var event = e || window.event;    #计算鼠标位于物体内部距离
		var disX = e.pageX - this.offsetLeft;
		var disY = e.pageY - this.offsetTop;
		var that = this;
		var iSpeedX, iSpeedY;
		document.onmousemove = function(e){   # 就是物体跟随鼠标位置
			var event = e || window.event;
			var newLeft = e.pageX - disX;
			var newTop = e.pageY - disY;
			iSpeedX = newLeft - lastX;      #通过计算出前后位置获得速度的初始值
			iSpeedY = newTop - lastY;
			lastX = newLeft;
			lastY = newTop;
			# var oSpan = document.createElement('span');
			# oSpan.style.left = newLeft + 'px';
			# oSpan.style.top = newTop + 'px';
			# document.body.appendChild(oSpan); 
			that.style.left = newLeft + 'px';
			that.style.top = newTop + 'px';
		}
		document.onmouseup = function(){
			document.onmousemove = null;
			startMove(that, iSpeedX, iSpeedY)
		}
	}
	function startMove(obj, iSpeedX, iSpeedY){
		clearInterval(obj.timer);
		# var iSpeedX = 50;
		# var iSpeedY = 60;
		var g = 9;
		obj.timer = setInterval(function(){
			iSpeedY += g;
			var nexLeft = obj.offsetLeft + iSpeedX;
			var nexTop = obj.offsetTop + iSpeedY;
			if(nexTop >= document.documentElement.clientHeight - obj.offsetHeight){
				iSpeedY *=  -1; #当物体到达底部，y轴上的力取反
				iSpeedY *= 0.8;  #不需要理想状态，来点摩擦力
				iSpeedX *= 0.8;
				nexTop = document.documentElement.clientHeight - obj.offsetHeight;
				#做融搓防止出现滚动条，让物体讯时等于底部。
			}
			if(nexTop <= 0){
				iSpeedY *= -1;
				iSpeedY *= 0.8;
				iSpeedX *= 0.8;
				nexTop = 0;
			}
			if(nexLeft >= document.documentElement.clientWidth - obj.offsetWidth){
				iSpeedX *= -1;
				iSpeedY *= 0.8;
				iSpeedX *= 0.8;
				nexLeft = document.documentElement.clientWidth - obj.offsetWidth;
				console.log(555)
			}
			if(nexLeft <= 0){
				iSpeedX *= -1;
				iSpeedY *= 0.8;
				iSpeedX *= 0.8;
				nexLeft = 0;
			}
			if(Math.abs(iSpeedX) < 1){
				iSpeedX = 0;
			}
			if(Math.abs(iSpeedY) < 1){
				iSpeedY = 0;
			}
			if(iSpeedX == 0  && nexTop == document.documentElement.clientHeight - obj.offsetHeight ){
				clearInterval(obj.timer);
				console.log(888)
			}
			obj.style.left = nexLeft + 'px';
			obj.style.top = nexTop + 'px';
		}, 30)
	}
</script>
```
通过利用鼠标的拖住速度。判定物体前后的距离获得初速度，获取视口值利用速度* -1 改变速度的方向位置

