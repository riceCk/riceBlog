---
title: NodeLess（一）
categories: 了解nodejs前世今生
tags: node系统学习
date: 2018-11-26 20:49:14  
---
# 一、后端的规范与思想
## 1.分层
*（1）Web层（接收和发送Http请求，封装；Web层、controller层）
    LoginController：（接收参数，判断是否非法，传给服务层）
*（2）业务逻辑层（服务层，XXXService）
    LoginService：（获取这个用户密码，进行比较）
*（3）DAO层：(Data Access Objec）
    DataBase（DB）：存数据
    业务：对对象进行操作
    如果要存储：对象转为数据
    如果要读取：数据转为对象
*（4）持久层：存在磁盘上
    文件：数据库
* （5）Domain：User

## 2、模块化
（1）导入和导出
```bash
module.exports == exports  
# true
```

## 3、nodejs的展现形式
```bash
function xzy(module, require, module, __filename, __dirname) {
    # ---
    # 中间的内容是我们写的nodejs代码
    # ---
    console.log(module)
    return module.exports
}
```

# 二、TCP/id协议
## Server服务器
* 事件
    listening
    connection
    close
    error
* 方法
    listen
    close
    address

```bash
let net = require('net')
# 网络层和运输层 -> TCP/IP协议
let server = net.createServer()
server.listen(3000, '127.0.0.1')  
# 监听服务器3000端口，ip：127.0.0.1
server.on('listening', () => {
    console.log(server.address())
    # address查看当前端口号和ip地址
    console.log('服务已启动')
})

server.on('connection', (socket) => {
    console.log('有新的连接')
    # 反馈请求的数据
})
```

## client客户端
### 事件&方法
#### connect事件
监听是否连接服务器
```bash
socket.on('connect', function () {
    console.log('已连接到服务器')
})
```
#### data事件
```bash
# client 客户端发请求
socket.write('hello server')
socket.on('data', function(data) {
    console.log(data.toString)
    # 客户端监听服务器发的请求
｝
# server 服务的监听
server.on('connection', (socket) => {
    socket.on('data', function (data) {
        console.log(data.toString())
        socket.write('hello client')
        # 服务器给客户端发请求
    })
})
```

#### end客户端关闭事件 & close服务的事件
```bash
# client 客户端收到信息就关闭
socket.on('data', function(data){
    console.log(data.toString())
    socket.end()
})
socket.on('end', function() {
    console.log('服务器已关闭')
})
# server 服务端
server.on('connection', (socket) => {
    socket.on('close', function() {
        console.log('客户端已关闭')
        server.close() # 服务端关闭服务
    })
})
```

#### setTimeout & timeout 客户端的超时事件方法
```bash
socket.setTimeout(2000)
# 设置2秒，服务器不返回就触发超时事件
socket.on('connect', function () {
    console.log('已连接到服务器')
})
socket.on('timeout', function() {
    console.log('超时了')
    socket.end()
})
```
### client四个属性
```bash
socket.on('connect', function () {
    console.log('已连接到服务器')
    console.log('远端地址：', socket.remoteAddress)
    console.log('远端端口:',socket.remotePort)
    console.log('远端地址：',socket.localAddress)
    console.log('远端端口：',socket.localPort)
})
```

# 三、http协议
## http & url
```bash
let http = require('http')
let url = require('url')
http.createServer(function(request, response) {
	let pathName = url.parse(request.url).pathname
	let pathms = url.parse(request.url, true).query
	console.log(pathName, pathms)
	# /index3.html,    { a: '1', b: '2' }
}).listen(3000)
```

# 四、mysql数据库
## 初次使用DataGrip软件连接
```bash
show databases ;   # 打开查看数据库
use mysql;         # 连接mysql库
show tables ;      # 打开显示mysql库里的表
desc db;           # 查看db表字段
show create table db;  # 查看创建db表的sql语句
select * from db;      # 查看表
create database school; # 创建库
```
## 字段类型
* 整数 int
* 长整数bigint(21)
* 浮点数 float
* 双精度浮点数double
* 字符串 varchar()
* 文本text

## 关于表的sql语句
注：一般开发用工具创建，但是至少需要认识
### 创建表
```bash
CREATE TABLE `student` (
  `id`      int(11)     NOT NULL AUTO_INCREMENT COMMENT 'student id',
  `stu_num` int(11)     NOT NULL                COMMENT 'student number',
  `name`    varchar(32) NOT NULL                COMMENT 'student name',
  `age`     int(11)     NOT NULL                COMMENT 'student age',
  `class`   int(11)     NOT NULL                COMMENT 'student class	',
  PRIMARY KEY (`id`),
  UNIQUE KEY `student_stu_num_uindex` (`stu_num`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1
```

### 添加表的新字段
```bash
alter table student
	add math int not null
	COMMENT 'student math';
```

### 删除表的字段
```bash
alter table student drop column math;
```
## 关于sql字段的语句
### 插入新数据
```bash
insert into student (`stu_num`, `name`, `age`, `class`)
       value(201307, "猴子", 18, 2);
```
### 修改
```bash
update student set age = 18, class = 3 where id = 2;
```
### 删除
```bash
delete from student where name="小猫"
``` m
### 统计数量
```bash
select count(*) from student where age=18;
```
### 求和
```bash
select sum(age) from student
```
### 平均数
```bash
select avg(age) as avg_age from student
```
### 分组
注：分组的class前面查看的字段也只能是class
```bash
select class, count(1) from student group by class;
```
### 倒叙排序
```bash
select * from student order by id desc;
```
### 分页/偏移量
```bash
# limit(第几页页数，每页数量)
select * from student limit 2,2;
```

# 五、node连接mysql
```bash
npm init # 先初始化
npm install mysql -save # 安装数据库
```