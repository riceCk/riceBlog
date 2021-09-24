---
title: Buffer模块（一）
categories: 了解nodejs前世今生
tags: node
date: 2018-07-24 14:51:14  
---
# 一、nodejs基本操作
## 1、通过官网安装包
## 2、通过nvm安装
[nvm GitHub下载地址](https://github.com/coreybutler/nvm-windows/releases)
安装过程有事是非常坎坷的，[大神nodejs和nvm拍坑安装流程](https://www.cnblogs.com/qiu-freedom/p/8871793.html)
### Nvm的命令
```bash
nvm list available # 
nvm install 6.14.2  #6.14.2版本号，安装指定版本
nvm use 6.14.2  #切换6点版本号
nvm list  #查看本机安装点所有node版本号
nvm uninstall 6.14.2  #删除6点的版本号
```
## 3、模块
AMD、CMD、CommonJS
### CommonJS
* 一个文件就是一个模块
* 每个模块内定义的变量、函数、对象，作用域都属于这个模块本身，其他模块无法访问
* 模块之间互相引用需要通过require()
* exports或者module.exports可以把对象暴露出来

#### exports和require的用法
```bash
# 模块一文件 module_01.js文件下
function add(a, b){
    return a + b;
}
var a = '1232132131'
var obj = new Object();
obj.name = 'ck';
obj.age = '12';
exports.add = add;
exports.a = a;
exports.obj = obj
# 模块二文件 module_02.js文件下
var res = require('./module_01')
console.log(res.add(2, 2))
console.log(res.a)
console.log(res.obj)
```
#### module.exports和require的用法
```bash
# 模块一文件 module_01.js文件下
var a = 'sdjfkaj'
module.exports = a;

# 模块二文件 module_02.js文件下
var res = require('./module_01')
console.log(res)
```

# 二、node常用API讲解（一）缓存和文件模块
## 1、Buffer简介
JavaScript 语言自身只有字符串数据类型，没有二进制数据类型。但在处理像TCP流或文件流时，必须使用到二进制数据。因此在 Node.js中，定义了一个Buffer 类，该类用来创建一个专门存放二进制数据的缓存区。可以让 Node.js 处理二进制数据，每当需要在 Node.js 中处理I/O操作中移动的数据时，就有可能使用 Buffer库。原始数据存储在 Buffer 类的实例中。一个 Buffer 类似于一个整数数组，但它对应于 V8 堆内存之外的一块原始内存。
## 2、Buffer 与字符编码
Buffer 实例一般用于表示编码字符的序列，比如 UTF-8 、 UCS2 、 Base64 、或十六进制编码的数据。 通过使用显式的字符编码，就可以在 Buffer 实例与普通的 JavaScript 字符串之间进行相互转换。unicode码 4字节 1bit 位 bye字节在v6.0之前创建Buffer对象直接使用new Buffer()构造函数来创建对象实例，但是Buffer对内存的权限操作相比很大，可以直接捕获一些敏感信息，所以在v6.0以后，官方文档里面建议使用 Buffer.from() 接口去创建Buffer对象。
### Node.js 目前支持的字符编码包括
* ascii - 仅支持 7 位 ASCII 数据。如果设置去掉高位的话，这种编码是非常快的。
* utf8 - 多字节编码的 Unicode 字符。许多网页和其他文档格式都使用 UTF-8 。
* utf16le - 2 或 4 个字节，小字节序编码的 Unicode 字符。
* ucs2 - utf16le 的别名。
* base64 - Base64 编码。
* latin1 - 一种把 Buffer 编码成一字节编码的字符串的方式。
* binary - latin1 的别名。
* hex - 将每个字节编码为两个十六进制字符。

Node.js自带的toString()方法不支持gbk，因此中文转换的时候需要加载第三方库，推荐下面这个编码转换库，iconvliticonv的纯js实现，支持的编码包括node.js原生编码：utf8, ucs2, ascii, binary,base64；同时支持广泛使用的单字节编码：Windows 125x family, ISO­8859 family,IBM/DOS codepages, Macintosh,family, KOI8 family, latin1, us­ascii；多字节编码：gbk, gb2313, Big5, cp950
###  安装iconvlit
```bash
$ npm install iconv-lite
```
# 三、Buffer常用操作

## 创建BUffer类
* let buffer = Buffer.alloc(10)  返回一个指定大小的 Buffer 实例，如果没有设置 fill，则默认填满 0
* let buffer = Buffer.from("大家好")  返回一个被 array 的值初始化的新的 Buffer 实例（传入的array 的元素只能是数字，不然就会自动被 0 覆盖）
* let buffer = Buffer.allocUnsafe(10) 返回一个指定大小的 Buffer 实例，但是它不会被初始化，所以它可能包含敏感的数据

### 实例简单运用
#### iconvlit插件的方法利用
```bash
let http = require('http')
let iconv = require('iconv-lite')

let str = "大家好"
let strGgb = iconv.encode(str,"gbk") # 将str编译成gbk格式
console.log(iconv.decode(strGgb,'gbk').toString); # 将gbk格式解码成utf8码
console.log(iconv.encodingExists("utf8")) # 判断插件iconv检查是否支持编码
```

####  类方法：Buffer.alloc(size[, fill[, encoding]])
**const buf = Buffer.alloc(11, 'hello world', 'utf-8')**
```bash
let http = require('http')
let iconv = require('iconv-lite')
#  let buffer = new Buffer(18)  已废弃

# 1个二进制 = 1bit
# 8个二进制数 = 1bytes(1b)
# b -> kb -> Gb-> Tb -> Eb -> Pb -> Zb
# buffer的定义赋值
let buffer = Buffer.alloc(10)  # buffer初始化
buffer[0] = 1;  #十进制
buffer[1] = 15;
buffer[2] = 255;
buffer[3] = 0xaa #十六进制
buffer[4] = 0x41 #字符串"A"
console.log(buffer) # => <Buffer 01 0f ff aa A 00 00 00 00 00>

# buffer编码输出类型，默认都输十进制
let buffer = Buffer.alloc(10)
buffer[0] = 0xa
console.log(buffer) # => <Buffer 0a 00 00 00 00 00 00 00 00 00> 十六进制存储
console.log(buffer[0].toString())    #=> 10 二进制输出
console.log(buffer[0].toString(16))  # => a 十六进制输出
```                                                                                         

#### let buffer = Buffer.from("大家好")    
```bash
# 这里编码三个中文字符变成9个字符这个是utf8的正常3字节转换中文，一般看到的两字节转换中文字符的是gbk国标编码格式
let buffer = Buffer.from("大家好")
console.log(buffer); # => <Buffer e5 a4 a7 e5 ae b6 e5 a5 bd>
console.log(buffer.toString()) # => 大家好 => toString是把二进制转换成urf8
```

#### let buffer = Buffer.allocUnsafe(10) 

```bash
let buffer = Buffer.allocUnsafe(10)  
# 创建buffer但是并没有初始化，内部有值占用
console.log(buffer)   # <Buffer 88 69 8a 8f 65 00 00 00 e8 69>
buffer.fill(0)   # 相当于初始化，用零填充
console.log(buffer)   # <Buffer 00 00 00 00 00 00 00 00 00 00>
```     
                                                                                                             
## 写入Buffer
### 语法
```bash
buf.write(string[, offset[, length]][, encoding])
```
### 参数
* string - 写入缓冲区的字符串。
* offset - 缓冲区开始写入的索引值，默认为 0 。
* length - 写入的字节数，默认为 buffer.length
* encoding - 使用的编码。默认为 'utf8' 。

**根据 encoding 的字符编码写入 string 到 buf 中的 offset 位置。 length 参数是写入的字节数。如果 buf 没有足够的空间保存整个字符串，则只会写入 string 的一部分。 只部分解码的字符不会被写入**与c语言写入类似，创造多少空间就使用多少见，溢出自动截掉

### 实例
```bash
let buf = Buffer.alloc(10);    #创建十个字节空间
let len = buf.write("hello world");
console.log("写入的字节数" + len); # => 10   len输出的是buf的字节数

buf.write("hello world", 2);  #第二个参数指定从第几个索引开始写入，默认0
console.log(buf.toString());  #=> hello wo  从索引第二位开始

buf.write("hello world", 2, 4) # 第三个参数指定写入几个字节
console.log(buf.toString());  #=> hello  只写入四个字节
```

## 读取buffer
### 语法
```bash
buf.toString([encoding[, start[, end]]])
```
### 参数
* encoding - 使用的编码。默认为 'utf8' 。
* start - 指定开始读取的索引位置，默认为 0。
* end - 结束位置，默认为缓冲区的末尾。

### 实例
```bash
buf = Buffer.alloc(26);
for (var i = 0 ; i < 26 ; i++) {
buf[i] = i + 97;
}
console.log(buf.toString());             # 输出: abcdefghijklmnopqrstuvwxyz
console.log(buf.toString('ascii'));      # 输出: abcdefghijklmnopqrstuvwxyz
console.log(buf.toString('ascii',0,5));  # 输出: abcde
console.log(buf.toString('utf8',0,5));   # 输出: abcde 
```
ascii码和utf8是互相兼容的 

## 将 Buffer 转换为 JSON 对象
### 语法
将 Node Buffer 转换为 JSON 对象的函数语法格式如下：
```bash
buf.toJSON()
```
### 实例 
当字符串化一个 Buffer 实例时，JSON.stringify() 会隐式地调用该 toJSON()。
```bash
const jsonBuff = Buffer.from([1,2,3,4])
const buf = Buffer.from([1,2,3,4]);
const json = JSON.stringify(buf);  #将json对象转化成json字符串
console.log(jsonBuff.toJSON());
# { type: 'Buffer', data: [ 1, 2, 3, 4 ] }   输出的是json对象格式
console.log(json);
# 输出: {"type":"Buffer","data":[1,2,3,4,5]}  输出的字符串格式
```

## 缓冲区合并
### 语法
```bash
Buffer.concat(list[, totalLength])
```
### 参数
* list - 用于合并的 Buffer 对象数组列表。
* totalLength - 指定合并后Buffer对象的总长度。

### 实例
```bash
var buffer1 = Buffer.from('渡一教育');
var buffer2 = Buffer.from('www.duyi.com');
var buffer3 = Buffer.concat([buffer1,buffer2]);
var buffer4 = Buffer.concat([buffer1,buffer2], 10);
# => 渡一教  一个汉字占三个字节，所以输出三个字
console.log("buffer3 内容: " + buffer3.toString())
# =>  buffer3 内容: 渡一教育www.duyi.com
```

## 缓冲区比较
### 语法
```bash
buf.compare(targetBuffer,[targetStart,targetEnd,sourceStart,sourceEnd]);
```
### 参数
* otherBuffer - 与 buf 对象比较的另外一个 Buffer 对象。
* targetStart - 数字, 可选 默认0
* targetEnd- 数字, 可选 默认: buffer.length
* sourceStart - 数字, 可选 默认0
* sourceEnd - 数字 默认: buffer.length

### 实例
```bash
# 比较a和A的字符编码大小
var buffer1 = Buffer.from('a');
var buffer2 = Buffer.from('A');
var result = buffer1.compare(buffer2);
# 以buffer1为基准，buffer1大 -> 1 ,反之自推
# 1 -> 大于   0 -> 等于  -1 -> 小于
console.log(result)  # => 1 
console.log(buffer1+'')  # => buffer1.toString  => a

# 参数问题 
var result = buffer1.compare(buffer2, 0, 1, 0, 1);
# 前两个控制buffer2的首位索引，后两个控制buffer1索引
```

## 拷贝缓冲区，深拷贝过程
### 语法
```bash
buf.copy(targetBuffer[, targetStart[, sourceStart[, sourceEnd]]])
```
### 参数
* targetBuffer - 要拷贝的 Buffer 对象。
* targetStart - 数字, 可选, 默认: 0
* sourceStart - 数字, 可选, 默认: 0
* sourceEnd - 数字, 可选, 默认: buffer.length

### 实例
```bash
var buf1 = Buffer.from('abcdefghijkl');
var buf2 = Buffer.from('zzzzzz');
# 将 buf2 插入到 buf1 指定位置上
buf2.copy(buf1, 2);
# abzzzzzzijkl  将buf2从索引第二位开始覆盖直到结束
```

## 缓冲区裁剪 指向的同一块地址，原地址改变之后会连带反应
### 语法
```bash
buf.slice([start[, end]])
```
### 参数
* start - 数字, 可选, 默认: 0
* end - 数字, 可选, 默认: buffer.length

### 实例
```bash
var buffer = Buffer.from("absdfjs")
var bufResult = buffer.slice(0, 3)
console.log(bufResult.toString()) #=> abs
buffer[0] = 41 # => bufResult也会发生改变
```

## 缓冲区长度
### 语法
```bash
buf.length;
```
### 实例
```bash
let str = "h汉"
let buff = Buffer.from(str)
console.log(str.length)   #字符串长度 => 2
console.log(buff.length)  # 字节长度 => 4
```

# 四、文件操作API
## 异步和同步
Node.js文件系统（fs模块）模块中的方法均有异步和同步版本，例如读取文件内容的函数有异步的fs.readFile（）和同步的fs.readFileSync（）。同步方法多有返回值
异步的方法函数最后一个参数为回调函数，回调函数的第一个参数包含啦错误信息（error）
**建议大家使用异步方法，比起同步，异步方法性能更高，速度更快，而且没有阻塞**
### 1、打开、读取文件方法
#### （1）同步
fs.openSync(path, flags[, mode])
* path <string> | <Buffer> | <URL> 路径
* flags <string> | <number>   
* mode <integer> Default: 0o666 权限
return readBytes 

**Flag Description**
* r 以读取模式打开文件。如果文件不存在抛出异常。
* r+ 以读写模式打开文件。如果文件不存在抛出异常。
* rs 以同步的方式读取文件。
* rs+ 以同步的方式读取和写入文件。
* w 以写入模式打开文件，如果文件不存在则创建。
* wx 类似 'w'，但是如果文件路径存在，则文件写入失败。
* w+ 以读写模式打开文件，如果文件不存在则创建。
* wx+ 类似 'w+'， 但是如果文件路径存在，则文件读写失败。
* a 以追加模式打开文件，如果文件不存在则创建。
* ax 类似 'a'， 但是如果文件路径存在，则文件追加失败。
* a+ 以读取追加模式打开文件，如果文件不存在则创建。
* ax+ 类似 'a+'， 但是如果文件路径存在，则文件读取追加失败。
![图片](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/nodeFlag.png)
```bash
var fs = require('fs')
#引入文件模块
var fd = fs.openSync('1.txt', 'r')
console.log(fd);
fs.closeSync(fd);
```

#### （2）异步打开 + 同步读取文件
**异步打开语法**
fs.open(path, flags[, mode], callback)
* path <string> | <Buffer> | <URL> 
* flags <string> | <number> 
* mode <integer> Default: 0o666 
* callback <Function> 
    err <Error>
    fd <integer>

**同步读取语法**
fs.readSync(fd, buffer, offset, length, position)

```bash
# buffer是内存空间; 0从buffer第几个字节开始读；
# buffer.length去多少个长度; 0从文件中第几个字节开始读，是字节
fs.open('1.txt', 'r', (err, fd) => {
    if(err)
        throw err
    var buffer = Buffer.alloc(10)
    var readBytes = fs.readSync(fd, buffer, 0, buffer.length, 0)
    console.log(buffer.toString());
    console.log(readBytes)
})
```
#### （3）异步打开 + 异步步读取文件
**异步读取语法**
fs.read(fd, buffer, offset, length, position, callback)
* fd <integer>  指定的文件中读取数据
* buffer <Buffer> | <Uint8Array>  是数据将被写入到的 buffer。
* offset <integer>   buffer 中开始写入的偏移量
* length <integer>  length 是一个整数，指定要读取的字节数。
* position <integer>   指定从文件中开始读取的位置，null，则数据从当 前文件读取位置开始读取
* callback <Function> 
    err <Error>     
    bytesRead <integer> 
    buffer <Buffer>

```bash
fs.open('1.txt', 'r', (err, fd) =>{
    var buffer = Buffer.alloc(10);
    fs.read(fd, buffer, 1, 3, 0,(err, byttesRead) =>{
        console.log(buffer.toString())
    })
})
```

#### 巩固知识题，在不明确文件中多少字节的情况下读取
```bash
fs.open('1.txt', 'r', (err, fd) =>{
    var buffer = Buffer.alloc(0);
    var buffer1 = Buffer.alloc(1)
    function sum(buffer, buffer1){         
        fs.read(fd, buffer1, 0, 1, buffer.length, (err, byttesRead) =>{
            if(byttesRead != 0){
                buffer = Buffer.concat([buffer, buffer1]);
                return sum(buffer, buffer1)
            }
            console.log(buffer.toString())
            return 
        })
    } 
    sum(buffer, buffer1) 
})
```

### 6、写入 
#### (1)、同步  
fs.writeSync(fd, buffer,[ offset[, length[, position]]]) 
* fd <integer> 
* buffer <Buffer> | <Uint8Array>  写入的文件内容
* offset <integer> 
* length <integer> 
* position <integer>指向从文件开始写入数据的位置的偏移量。 如果 typeof position !==  'number'，则数据从当前位置写入。
* Returns: <number>

#### (2)、异步 
fs.write() 
* fd <integer> string <string> || <buffer>
* position <integer> 
* encoding <string> 该参数只有在写入数据是字符串的时候才生效。 
* callback <Function> 
    err <Error> 
    written <integer> 指定传入的字符串被写入多少字节
    string <string>
    
异步实例
先打开在写入
```bash
fs.open('1.txt.bak', 'w', (err, fd) =>{
    fs.write(fd, "holler world", (err, written) =>{
        if(err)
            throw err
        console.log("写入成功")
    })
})
```
### 3、简单读取文件 
#### (1)、同步
fs.readFileSync(path[, options]) 
```bash
var buffer = fs.readFileSync('1.txt', {flag: 'r'})
```
#### (2)、异步
fs.readFile(path[, options], callback)
* path <string> | <Buffer> | <URL> | <integer> 文件名或文件描述 符。 
* options <Object> | <string> encoding <string> | <null> 默认为 null。
* flag <string> 详见支持的文件系统flag。默认 为 'r'。 
* callback <Function> err <Error> data <string> | <Buffer>
```bash
# 不设置flag默认也是 r
fs.readFile('1.txt', (err, buffer) => {
    console.log(buffer.toString())
})
```
### 4、简单写取文件 
#### (1)、同步
fs.writeFileSync(file, data[, options])
fs.writeFile() 的同步版本。返回 undefined。
#### (2)、异步
fs.writeFile(file, data[, options], callback)
异步地写入数据到文件，如果文件已经存在，则替代文件。 
* file：文件名或文件描述符 
* options.encoding: data编码，默认'utf8' 
* options.mode:文件属性，默认0o666 data 可以是一个字符串或一个 buffer。 
    如果 data 是一个 buffer，则忽略 encoding 选项。它默认为 'utf8'

```bash
# 但定义buffer时，在传入encoding编码格式就变得无效啦
var buffer = Buffer.from('我是被写入的数据')
fs.writeFile('1.txt', buffer, {flag: 'a', encoding: 'ascii'}, (err) =>{
    console.log('数据写入成功')
})
```

### 5、创建读取流、写入流

#### 读取流
fs.createReadStream(path[, options])返回一个新建的 ReadStream 对象 
* path：文件路径 
* options：选项，包含以下值 
    flags：文件属性。默认：'r' 
    encoding:文件编码，默认 null 
    fd：文件描述符，如果指定了 fd，则会忽略 path 参数。 这意味着不会触发 'open' 事件。 mode：文件读写属性，默认0o666 
    autoClose：如果 autoClose 为 false，则文件描述符不会被关闭，即使有错误。默认为 true。 
    start：指定读取范围的开始位置 end：指定读取范围的结束位置
**fs.ReadStream 类**
fs.ReadStream 是createReadStream返回的可读流
close 事件 ReadStream 底层的文件描述符被关闭时触发
open 事件 当 ReadStream 的文件被打开时触发。

#### 写入流
fs.createWriteStream(path[, options])
返回一个新建的 WriteStream 对象 
* path：文件路径 
* options：选项，包含以下值
     flags：文件属性。默认：'w' encoding:文件编码，默认 utf8 fd：文件描述符，如果指定了 fd，则会忽略 path 参数。 这意味着不会触发 'open' 事件。 
     mode：文件读写属性，默认0o666 
     autoClose：如果 autoClose 为 false，则文件描述符不会被关闭，即使有错误。默认为 true。 
     start：指定文件写入的开始位置
     
**fs.WriteStream 类** 
fs.WriteStream 是 createWriteStream 返回的可写流
`writeStream.path` 流正在写入的文件的路径，指定在 fs.createWriteStream() 的第一个参数。 如果 path 传入的是一个字符串，则 writeStream.path 是一个字符串。 如果 path 传入的是一个 Buffer，则 writeStream.path 是一个 Buffer。

#### 实例
```bash
# 正常的读写流操作
var rs = fs.createReadStream('1.txt',{highWaterMark: 3})
var ws = fs.createWriteStream('2.txt')
rs.once('open', () =>{
    console.log("读取流已打开")
})
rs.once('close', () =>{
    console.log("读取流已关闭")
    ws.end()
})
rs.on('data', (chunk) =>{
    console.log(chunk.toString())
    ws.write(chunk)
})
ws.on('open', () => {
    console.log("写入流已打开")
})
ws.on('close', () => {
    console.log("写入流已关闭")
})
ws.on('finish', () => {
    console.log("写入流已结束")
})

# 管道方法 实现数据自动传输
var rs = fs.createReadStream('1.txt',{highWaterMark: 3})
var ws = fs.createWriteStream('2.txt')
rs.pipe(ws)
```
### 6、查看文件信息 
fs.statSync(path) 
#### 同步的 fs.stat。 
返回一个 fs.Stats 实例。 传递不存在的文件地址会抛出异常。
#### 异步fs.stat(path, callback) 
该方法返回关于文件的信息 path：文件地址 回调有两个参数 (err, stats) 
其中 stats 是一个 fs.Stats 对象。 不建议在调用 fs.open() 、fs.readFile() 或 fs.writeFile() 之前使用 fs.stat() 检查一个文件是 否存在。 作为替代，用户代码应该直接打开/读取/写入文件，当文件无效时再处理错误。 传递不存在的文件地址会抛出异常。
#### stats类方法
* stats.isFile()	
如果是文件返回 true，否则返回 false。

* stats.isDirectory()	
如果是目录返回 true，否则返回 false。

* stats.isBlockDevice()	
如果是块设备返回 true，否则返回 false。

* stats.isCharacterDevice()	
如果是字符设备返回 true，否则返回 false。

* stats.isSymbolicLink()	
如果是软链接返回 true，否则返回 false。

* stats.isFIFO()	
如果是FIFO，返回true，否则返回 false。FIFO是UNIX中的一种特殊类型的命令管道。

* stats.isSocket()	
如果是 Socket 返回 true，否则返回 false。

#### 实例一
```bash
var fsState = fs.statSync('1.txt')
console.log(fsState)
```

#### 实例二
```bash
var fs = require('fs');
fs.stat('./fs.js', function (err, stats) {
    #如果是文件返回 true
    console.log(stats.isFile());         #true
})
```

### 7 、判断文件是否存在 
fs.existsSync(path) 
如果文件存在，则返回 true
```bash
var fsState = fs.existsSync('1.txt')
console.log(fsState)
```
### 8、删除文件 
**同步**
fs.unlink(path, callback) 删除一个符号链接 
**异步**
fs.unlinkSync(path, callback)
* path：链接的存储地址 
* callback：完成后的回调函数，携带 err 参数

实例
```bash
fs.unlinkSync('2.txt')
```
### 9、fs.realpath(path[, options], callback) 
此方法将相对地址转换为绝对地址。 
* path：需要转换的地址 
* options：字符编码，默认'utf8' callback：callback 有两个参数 (err, resolvedPath)。 
    传递不存在的相对路径会抛出异常。 除此方法外，也可以使用 process.cwd 来获取当前目录绝对路径，从而实现转换功能。

```bash
fs.realpath('1.txt')
```
### 10、创建一个文件夹 
fs.mkdir(path[, mode], callback) 该方法用来创建目录，与mkdir命令的作用一致。 
* path：目录地址 mode：目标属性，建立目录的同时设置目录的权限，默认：0o777 
* callback：创建完成后执行的回调函数，带有一个异常参数。

语法
```bash
fs.mkdirSync(path[, mode])
```

### 11、删除一个文件夹 
fs.rmdir(path, callback) 该方法用来删除空目录，与rmdir命令的作用一致。 如果删除的目录不是空目录，会抛出错误。 
* path：目录地址 
* callback：完成后执行的回调函数，带有一个异常参数。 注意：在文件上调用此方法，会导致错误。

```bash
fs.rmdirSync(path)
```