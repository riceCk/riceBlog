---
title: Http模块（二）
categories: 了解nodejs前世今生
tags: node
date: 2018-07-24 14:51:14  
---

# 五、node的http协议
HTTP协议是Hyper Text Transfer Protocol（超文本传输协议）的缩写,是用于从万维网 （WWW:World Wide Web ）服务器传输超文本到本地浏览器的传送协议。
# Server 实例
```bash
const http = require('http')
const server = http.createServer()
```
## request 
事件 每次接收到一个请求时触发
### listen监听
启动一个HTTP服务监听connections，它与net模块下的server.listen()使用方式相同。启动的服务可能是一个 TCP 或者 一个 IPC server，取决于监听的内容。我们主要介绍TCP servers的创建方式：
* server.listen([port][, host][, backlog][, callback])
* port：要监听的端口
* host：域名
* backlog：待连接队列的最大长度
* callback：此方法将会被添加为’listening’ 事件的监听器。

### response.end()
该方法会通知服务器，所有响应头和响应主体都已被发送，即服务器将其视为已完成。每次响应都必须调用 response.end() 方法。
```bash
const http = require('http')
const server = http.createServer()
server.on('request', (req, res) => {})
httpServer.listen(8061, '127.0.0.1', () => {}
```

#### 上面两个事件整合一下
```bash
http.createServer((req, res) => {
  console.log(req.headers)
  #添加响应头，content-type是这是编码格式
  res.setHeader("content-type","text/html;charset=utf-8")
  # 服务器写入内容
  res.write("<h1>你好服务器已访问成功</h1>")
  res.end()
}).listen(3000, () => {
  console.log("服务器已启动")
})
```

### close 事件 
在 response.end() 被调用或能够刷新之前被终止时触发。

### response.setHeader(name, value) 
提供了对响应对象的处理
### response.write(chunk[, encoding][, callback]) 
该方法会发送一块响应主体。 它可被多次调用，以便提供连续的响应主体片段。
* chunk：chunk 可以是一个字符串或一个 buffer。 如果 chunk 是一个字符串，
则第二个参数指定如何将它编码成一个字节流。
* encoding： 默认为 ‘utf8’。
* callback：当数据块被刷新时，callback 会被调用。
如果全部数据被成功刷新到内核缓冲区，则返回 true。 如果全部或部分数据还在内存中排队，则返回 false。当缓冲区再次空闲时，则触发 ‘drain’ 事件。 
```bash
# 对文本/html标签进行编码
res.setHeader("content-type","text/html;charset=utf-8")
res.write("<h1>你好服务器已访问成功</h1>")

# 下载的文件
res.setHeader("content-type","application/octet-stream")
#内容一个描述，如果是下载时可以进行文件名的指定
res.setHeader("Content-Disposition", "filename=hello.html")
res.write("<h1>你好服务器已访问成功</h1>")
```
#### ETag            
请求变量的实体标签的当前值                           
ETag: “737060cd8c284d8af7ad3082f209582d”
```bash
res.setHeader("etag", "737060cd8c284d8af7ad3082f209582d")
```
相当于给网站添加一个唯一的id，当再一次对服务器请求的时候，通过比对之前网站的服务器If-None-Match的值进行比对，如果一样就是说之之前的网站，还在缓存中。 

#### 设置网页图标
```bash
const http = require('http')
const fs = require('fs')
http.createServer((req, res) => {
  if(req.url === '/favicon'){
    res.setHeader("content-type", "image/png")
    const rs = fs.createReadStream('1.png');
    rs.pipe(res)
  }else{
    res.setHeader("content-type","text/html;charset=utf-8")
    res.setHeader("etag", "737060cd8c284d8af7ad3082f209582d")
    res.write("<h1>你好服务器已访问成功</h1>")
    res.end()
  }  
}).listen(3000, () => {
  console.log("服务器已启动")
})
```
#### refresh
应用于重定向或一个新的资源被创造，在5秒之后重定向由网景提出，被大部分浏览器支持）
```bash
res.setHeader("Refresh","3;url=http://www.163.com")
```
#### 外部文件的引入，写入流操作
```bash
const http = require('http')
const fs = require('fs')
http.createServer((req, res) => {
  res.setHeader("content-type","text/html;charset=utf-8");
  fs.createReadStream("index.html").pipe(res);
}).listen(3000, () => {
  console.log("服务器已启动")
})
```

#### 简单的做一个用户登录
```bash
#html中
<form action="login" method="POST" enctype="application/x-www-form-urlencoded">
  用户名：<input type="text" name="username" id="username"> <br/>
  密码： <input type="password" name="password" id="password"> <br/>
  <button>提交</button>
</form>
# js服务器
const http = require('http')
const fs = require('fs')
http.createServer((req, res) => {
  if(req.url==='/'){
    res.setHeader("content-type","text/html;charset=utf-8")
    fs.createReadStream("index.html").pipe(res);
  }else if(req.url.indexOf('login')){
    req.on("data", (chunk) => {
      console.log(chunk.toString());
    })
    res.end()
  }
}).listen(3000, () => {
  console.log("服务器已启动")
})

```

### message.httpVersion
该属性返回客户端发送的HTTP版本
### message.method 
返回一个字符串，表示请求的方法。 该属性只读
### message.rawHeaders
该属性返回接收到的原始的请求头或响应头列表。
```bash
httpServer.on('request', (req, res) => { 
  console.log(req.httpVersion); # 1.1 
  console.log(req.method)  #GET
  console.log(req.rawHeaders) #[...]
  res.end('success'); 
});
```
# http.request 
Node.js 为每台服务器维护多个连接来进行 HTTP 请求。 该函数允许显式地发出请求。 options：可以是一个对象、或字符串、或 URL 对象。 如果 options 是一个字符串，它会被自 动使用 url.parse() 解析。 如果它是一个 URL 对象, 它会被默认转换成一个 options 对象。 callback：callback 参数会作为单次监听器被添加到 ‘response’ 事件 http.request() 返回一个 `http.ClientRequest `类的实例。
### 实例一
创建host方法，对服务器进行请求
```bash
const  http  =  require('http'); 
const  postData  =  JSON.stringify({  msg :  'Hello World!' }); 
const  options  =  {
  protocol: 'http:',    
  # 使用的协议。默认为 http:   
  host:'127.0.0.1',    
  # 请求发送至的服务器的域名或 IP 地址。默认为 localhost     
  hostname: '127.0.0.1',    
  #  host 的别名。为了支持 url.parse()，hostname 优先 于 host。     
  family:'4', 
  # 当解析 host 和 hostname 时使用的 IP 地址族。 
  #有效值是 4 或 6。当未指定 时，则同时使用 IP v4 和 v6。        
  port:3000,
  # 远程服务器的端口。默认为 80。     
  localAddress:'',
  # 为网络连接绑定的本地地址。     
  socketPath:'',
  #  Unix 域 Socket（使用 host:port 或 socketPath）。    
  method:'POST',
  # 指定 HTTP 请求方法的字符串。默认为 'GET'     
  path: '/user',
  # 请求的路径。默认为 '/'。 应包括查询字符串（如有的话）。如  '/index.html?page=12'。      
  headers: {
    # 包含请求头的对象。       
    'Content-Type': 'application/x-www-form-urlencoded',       
    'Content-Length': Buffer.byteLength(postData)     
  },     
  auth:'xiaoqiang:123123',
  # 基本身份验证，如 'user:password' 用来计算 Authorization  请求头。     
  agent:false,
  # 控制 Agent 的行为。 可能的值有Agent对象，false,undefined     
  timeout:1000,
  #  指定 socket 超时的毫秒数。 它设置了 socket 等待连接的超时时间。 
};
  const req  =  http.request(options,  (res)  =>  { 
    console.log(`状态码: ${res.statusCode}`); 
    console.log(`响应头: ${JSON.stringify(res.headers)}`); 
});
# 如果请求过程中遇到任何错误,则在返回的请求对象中会触发 'error' 事件。  
# 对于所有的 'error' 事件，如果没有注册监听器，则抛出错误。 
req.on('error', (e) => {     
  console.error(`请求遇到问题: ${e.message}`); 
});
#  写入数据到请求主体 
req.write(postData); 
req.end();     
# 使用 http.request() 必须调用 req.end() 来表明请求的结束，即使没有 数据被写入请求主体。

## 配置一个服务器
var http = require('http');
http.createServer((req, res) =>{
  req.on('data', (chunk) =>{
    console.log(chunk.toString())
  })
  res.write('received');
  res.end();
}).listen(3000)
```

### 实例二
```bash
const  http  =  require('http'); 
http.get('http://127.0.0.1:3000',  (res)  =>  {
  const  {  statusCode  }  =  res; 
  if  (statusCode  !==  200)  { 
    console.error('请求失败。\n'  +  `状态码: ${statusCode}`); 
  } 
  res.setEncoding('utf8'); 
  let  rawData  =  ''; 
  res.on('data',  (chunk)  =>  {  
    rawData  +=  chunk;  
  }); 
  res.on('end',  ()  =>  {
    console.log(rawData);
  });
}).on('error',  (e)  =>  { 
  console.error(`错误: ${e.message}`); 
});
```
## http.ClientRequest 类 
该对象在 http.request() 内部被创建并返回。 它表示着一个正在处理的请求，其请求头已进入队列。 请求头仍可使用 setHeader()、 getHeader() 和 removeHeader() API 进行修改。 实际的请求头会与第一个数据块一起发送或当调用 request.end() 时发送 要获取响应，需为请求对象添加一个 ‘response’ 事件监听。 当响应头被接收到时，请求对象 会触发 ‘response’ 事件 。 ‘ response’ 事件被执行时带有一个参数，该参数是一个 http.IncomingMessage 实例。 如果没有添加 ‘response’ 事件处理函数，则响应会被整个丢弃。 如果添加了 ‘response’  事件处理函数，则必须消耗完响应对象的数据， 可通过调用 response.read()、或添加一个 ‘data’ 事件处理函数、或调用 .resume() 方法。 数据被消耗完时会触发 ‘end’ 事件。 在数据被读取完之前会消耗内存，可能会造成  ‘process out of memory’ 错误。 注意：Node.js 不会检查 Content-Length 与已传输的请求主体的长度是否相等。
## response 事件 
当请求的响应被接收到时触发。 该事件只触发一次。
## request.write(chunk[, encoding][, callback])
* chunk:要发送的字符串或Buffer 
* encoding：仅当 chunk 是一个字符串时才有效。默认为 ‘utf8’。 
* callback：当数据块被刷新时调用

## request.end([data[, encoding]][, callback]) 
结束发送请求， 如果部分请求主体还未被发送，则会更新它们到流中。 
* data:如果指定了 data，则相当于调用 request.write(data, encoding) 之后再调用  request.end(callback)。
* callback:当请求流结束时会被调用。

## request.abort() 
标记请求为终止。 调用该方法将使响应中剩余的数据被丢弃且 socket 被销毁。
### request.aborted 
如果请求已被终止，则该属性的值为请求被终止的时间，从 1 January 1970 00:00:00 UTC 到 现在的毫秒数。
### abort 事件
当请求已被客户端终止时触发。 该事件仅在首次调用 abort() 时触发  
### 实例
```bash
req.on('abort', () => {   
  console.log(req.aborted); 
}); 
req.abort();
```

## request.setHeader(name, value) 
为 headers 对象设置一个单一的 header 值。如果该 header 已经存在了，则将会被替换。 这里使用一个字符串数组来设置有相同名称的多个 headers。 
```bash
req.setHeader('Content-Type','application/json'); 
req.setHeader('Set-Cookie', ['type=ninja', 'language=javascript']);
```
## connect 事件 
每当服务器响应 CONNECT 请求时触发。 如果该事件未被监听，则接收到 CONNECT 方法的客户端会关闭连接。
```bash
const req = http.request(options); 
req.end();   
req.on('connect', (res, socket, head) => {     
  console.log('已连接');
});
```
## 封装一个简单的豆瓣电影爬虫
```bash
var https = require('https');
var fs = require('fs');
var path = require('path');
var url = 'https://movie.douban.com/j/search_subjects?type=movie&tag=%E8%B1%86%E7%93%A3%E9%AB%98%E5%88%86&page_limit=20&page_start=0'
function request(url){
  var promise = new Promise((resolve,reject) =>[
    https.get(url, (res) => {
      var rawData = [];
      res.on("data", (chunk) => {
        rawData.push(chunk);
      })
      res.once("end", () => {
        var img = Buffer.concat(rawData);
        resolve(img)
      })
    })
  ])
  return promise;
}
request(url).then(rawData => {
  var result = JSON.parse(rawData);
  result.subjects.forEach(item => {
    request(item.cover).then((img) => {
      fs.writeFile(`${item.title}${path.extname(item.cover)}`, img, (err) =>{
        console.log("写入成功")
      })
    })
  });
})
```

# path
## path.delimiter 
该属性的值为当前系统的路径分隔符。Windows下是：";" POSIX下是：":"
```bash
console.log(path.delimiter); # -> ;
```

## path.sep 
该属性的值为当前平台路径分隔符。Windows下为："\" POSIX下为："/" 

## path.basename(path[, ext])
该方法返回一个 path 的后一部分，类似于 unix 中的 basename 命令。 
path：路径字符串 ext： 文件扩展名 
```bash
console.log(path.basename('/Users/xiaoqiang/Documents/work/demo/NodeApi/app.js')) 
# app
console.log(path.basename('/Users/xiaoqiang/Documents/work/demo/NodeApi/app.js',  '.js'));
# app.js
```
## path.dirname(path)
该方法返回一个路径中的目录，类似于Unix中的dirname命令。 
```bash
console.log(path.dirname("/Users/xiaoqiang/Documents/work/demo/NodeApi/app.js"));
# /Users/xiaoqiang/Documents/work/demo/NodeApi/
```
## path.extname(path) 
该方法返回地址的中的文件扩展名，如果路径中没有文件扩展名，则返回空字符串。
```bash
console.log(path.extname("/Users/xiaoqiang/Documents/work/demo/NodeApi/app.js")); 
# .js
```
## path.parse(path)
该方法对一个地址进行格式化，返回一个包含dir root base name ext属性的对象
```bash
console.log(path.parse("/Users/xiaoqiang/Documents/work/demo/NodeApi/app.js"));
# 输出如下
{ root: '/',
  dir: '/Users/xiaoqiang/Documents/work/demo/NodeApi',
  base: 'app.js',
  ext: '.js',
  name: 'app'
}
```

## path.format(pathObject) 
该方法作用与 path.parse(path) 方法相反，用来将一个特定对象转换为字符串路径。 
```bash
let pathStr = path.format({   
  root: '/',   
  dir: '/Users/xiaoqiang/Documents/work/demo/NodeApi',   
  base: 'app.js',   
  ext: '.js',   
  name: 'app'  
}); 
console.log(pathStr);
# /Users/xiaoqiang/Documents/work/demo/NodeApi\app.js
```
## path.isAbsolute(path) 
该方法会判断一个路径是不是绝对路径。

## path.join([…paths])
该方法使用当前平台的地址分隔符把给定的地址片段连接到一起。
```bash
console.log(path.join('/Users', 'xiaoqiang', 'Documents/work/demo/NodeApi', 'app.js'));
#\Users\xiaoqiang\Documents\work\demo\NodeApi\app.js
console.log(path.join('/Users/xiaoqiang/../work/demo/./app.js'));
#\Users\work\demo\app.js
```

## path.relative(from, to) 
该方法会返回两个地址参数from到to之间的相对路径，如果from与to为同一个路径，则返回一 个空字符串。 
```bash
console.log(path.relative('/Users/xiaoqiang/Documents/work/demo/NodeApi','/Users/xiaoqiang/Documents/work/demo'))
# ../
```
## path.resolve([…paths])
该方法用来将多个路径片段解析成一个绝对路径。 解析顺序由右至左，如果解析结束后还未生成一个绝对路径，会将当前路径拼接到解析好的路径上。 
```bash
console.log(path.resolve('/Users', 'xiaoqiang', 'demo/NodeApi'));
#\Users\xiaoqiang\demo\NodeApi 
console.log(path.resolve('/Users', 'xiaoqiang', '/demo/NodeApi')); 
#\demo\NodeApi
console.log(path.resolve('xiaoqiang', 'demo/NodeApi'));
# \Users\大帅比\Desktop\nodejs\xiaoqiang\demo\NodeApi
```

