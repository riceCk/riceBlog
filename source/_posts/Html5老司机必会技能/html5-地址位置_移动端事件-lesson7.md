---
title: html5-地址位置_移动端事件-lesson7
categories: Html5老司机必会技能
tags: HTML5
date: 2019-05-26 11:37:50
---
# geolocation 获取用户地理位置信息
## getCurrentPosition()   获取当前的位置信息
* success回调  (必须)
* error回调
* options参数

```bash
console.log(window.navigator.geolocation)
function success (e) {
  console.log('success', e)
}
function error (e) {
  console.log('error', e);
}
let options = {
  timeout: 1000, 
  # 请求超时时间 
  enableHighAccuracy: true 
  #是否需要高精度位置默认false
  maximumAge: 0 
  #位置信息过期时间 设置为0就无条件获取新的地理位置信息 默认0  
}
window.navigator.geolocation.getCurrentPosition(success, error, options);
```
### success 成功回调信息
* latitude 纬度
* longitude 经度
* altitude 海拔
* accuracy 定位精准度，单位m
* altitudeAccuracy 海拔精准度，单位m
* heading 方向
* speed 速度

![获取地理位置信息](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/geopositiion.png)

## watchPosition() 监视位置变化，和1参数一样
## clearWatch()  清除位置监视
监听地理位置实时刷新
```bash
function success (e) {
  console.log('success', e)
}
function error (e) {
  console.log('error', e);
}
let options = {}
let watcher = window.navigator.geolocation.watchPosition(success, error, options);
window.navigator.geolocation.clearWatch(watcher) # 清除
```

# devicemotion 监听设备加速度变化
均为只读属性
* 1.accelerationIncludingGravity(x, y, z) （
包括重心引力,z轴方向加了9.8,在x,y方向上的值两者相同）重力加速度
* 2.acceleration(x, y, z) 加速度（不包含重力加速度）
（需要陀螺仪支持）
* 3.rotationRate(alpha, beta, gamma)旋转速率
* 4.interval  获取的时间间隔

## 实例摇一摇
```bash
let speed = 25;
let lastTime = 0;
let lastX = 0, lastY = 0, lastZ = 0;
window.addEventListener('devicemotion', function (event) {
  console.log(event, event.acceleration.x, event.acceleration.y); 
  # 加速度
  console.log(event, event.accelerationIncludingGravity.x, event.accelerationIncludingGravity.y); # 重力
  let nowTime = (new Date()).getTime();
  let x = event.acceleration.x
  let y = event.acceleration.y
  let z = event.acceleration.z
  if (nowTime - lastTime > 500) {
    lastTime = nowTime
    if (Math.abs(x - lastX) > speed || Math.abs(y - lastY) > speed || Math.abs(z - lastZ)) {
      console.log('摇一摇')
      lastX = x;
      lastY = y;
      lastZ = z;
    }
  }
})
```

# deviceorientation监听设备在方向上的变化
事件源对象属性
* 1.alpha 表示设备沿z轴上的旋转角度，范围为0~360 正北0
* 2.beta 表示设备在x轴上的旋转角度，范围为-180~180。它描述的是设备由前向后旋转的情况。
* 3.gamma 表示设备在y轴上的旋转角度，范围为-90~90。它描述的是设备由左向右旋转的情况。
* 4、webkitCompassHeading：与正北方向的角度差值。正北为0度，正东为90度，正南为180度，正西为270度。因为0度是正北，所以叫指北针。ios
* 5、webkitCompassAccuracy：指北针的精确度，表示偏差为正负多少度。一般是10。

```bash
window.addEventListener('deviceorientation', function (e) {
  console.log(e.alpha) 
  console.log(e.beta)
  console.log(e.gamma)
})
```
 

