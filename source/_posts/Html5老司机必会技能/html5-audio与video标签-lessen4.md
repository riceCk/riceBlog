---
title: html5-audio/video标签-lesson4
categories: Html5老司机必会技能
tags: HTML5
date: 2019-05-17 15:40:50
---
# 标签属性
## controls添加控件属性
```bash
<audio src="./1.mp3" controls></audio>
<video src="./5.mp4" controls></video>
```
## autopla 自动播放
在谷歌浏览器向静态页面方式噪音装置，不会进行自动播放，网络请求下可以,其他浏览器可以
```bash
<audio src="./1.mp3" controls autoplay></audio>
<video src="./5.mp4" controls autoplay></video>
```
## preload(node/metadata/auto) 预加载
规定是否在页面加载后再入音频,与autopla自动播放属性冲突（二选一）
* none 不需要加载数据
* metadata 元数据诸如时长、比特率、帧大小这样的原数据，而不是媒体内容需要加载的
* auto浏览器应当加载它认为适量的媒体内容
```bash
<audio src="./1.mp3" controls preload="none"></audio>
```
## loop循环播放
```bash
<audio src="./1.mp3" controls loop></audio>
```
## poster(video独有) 海报封面
```bash
<video src="./5.mp4" controls autoplay poster="./0.jpg"></video>
```
## 多类型资源
以防浏览器对音频或者视频文件格式不兼容问题，采用的兼容性处置
```bash
<audio id="music" controls>
  <source src="1.mp3" type="audio/mpeg">
  <source src="1.ogg" type="audio/ogg">
</audio>
```
# 脚本化
## 脚本化创建标签
```bash
let audio = document.createElement('audio');
# let audio = new Audio(); 是可以的，但是video不可以
audio.setAttribute('controls', 'controls');
audio.setAttribute('autoplay', 'autoplay')
audio.loop = 'loop' # audio特有属性所以有效
audio.src = '1.mp3';
document.body.appendChild(audio);
```
## 方法
* paly() 播放
* pause() 暂停
* load()  重新加载

```bash
let audio = document.createElement('audio');
audio.play() # 播放
audio.pause() # 暂停
audio.load() 
# 重新加载视频/音频，用于更改来源或者其他设置后对音频/视频元素进行更新
```
## 属性调节
* volume  调节音量
* playbackRate 调节播放速率
* currentTime 设置或者返回音频/视频播放当前位置
* duration 返回当前音频/视频的时长
* played/buffered/serkable 当前缓存的进度
**palyed**属性返回已经播放（看过）的时间段，**buffered**属性返回当前以及缓冲的时间段，**seekable**属性则返回用户可以跳转的时间段，这三个属性都是TimeRanges对象，每个对象都有一个**length**属性以及**star()**和**end()**方法，length属性表示当前的一个时间段，**star()**和**end()**分别返回当前时间段的起始时间点和结束时间点（单位是秒，起始参数0）
* paused/seeking/ended 
**paused**为true表示播放器暂停，否则false；**seeking**为true表示播放器正在调到一个新的播放点，如果播放器播放完媒体并且停下来**ended**属性为true

```bash
audio.volume -= 0.3 
# 调节音量的，0<volume<1
audio.playbackRate += .5
# 调节播放速率 0~1表示慢放，1-16表示快发
audio.currentTime = 50
audio.duration 
# 200.620408
```
###  readyState音频的当前就绪状态
* 0：没有关于音频是否就绪的信息
* 1：关于音频就绪的元数据
* 2：关于当前播放位置的数据可用的，但没有足够的数据来播放下一帧/毫秒
* 3：当前及至少下一帧的数据可用的
* 4：可用数据足以开始播放

###  networkState 属性返回音频的当前网络状态
* 0：音频尚未初始化
* 1：音频是活动的且已选取资源，但并未使用网络
* 2：浏览器正在下载数据
* 3：未找到音频来源

###  error事件在音频/视频（audio/video）加载发生错误时触发
* 1：取回过程被用户中止
* 2：下载时发生错误
* 3：当解码时发生错误
* 4：不支持音频/视频

## 监听事件
* canpalyType() 判断音频/视频格式的浏览器兼容问题
* play 开始播放触发
* pause 暂停触发
* loadedmetadata 浏览器获取完媒体的元数据触发
* loadeddata 浏览器已经加载完当前帧数据，准备播放时触发，主要IE8
* ended 当前播放结束后触发

```bash
let audio = new Audio()
if (a.canpalyType('audio/mp3')) {
  # 当前浏览器兼容在进行下面
  audio.src = '.1/mp3';
  audio.play();
}
audio.onplay = function (e) {
  console.log('play', e)
}
audio.onpause =function (e) {
  console.log('onpause', e)
}
audio.onloadedmetadata = function (e) {
  console.log('metadata', e);
}
```
