---
title: Linux基本指令
categories: Linux从入门到精通
tags: Linux指令
date: 2018-07-23 09:47:00
---


# Linux的分类
## 1.Linux起源
Unix诞⽣，最初的计算机都是批处理式的，⼈们希望能够多⼈协同⼀起进⾏⼯作，所以希望研发⼀个多路信息计算系统。所以就产⽣了⼀个计划，叫Multics（Multiplexed informtion and Computing Service）。**1965年⻉尔实验室（Bell）、麻省理⼯学院（MIT）以及通⽤电⽓（GE)**联合发起的项⽬。但是这个项⽬⼀个都进展缓慢，后来⻉尔实验室就退出了。Ken Thompson在他们研发Multics的时候，写了⼀个太空⼤战的游戏。但是随着Bell实验室的退出，他的这个打⻜机的游戏就没有可以运⾏的平台了。所以这个⼈就⾃⼰写了⼀个⼩系统。当这个⼈写完这个⼩系统了之后，就⼀脸兴奋的叫同事⼀起过来玩。但是他的同事并没有对他的打⻜机的游戏感兴趣，反⽽对他写的系统感兴趣。然后他们把这个操作系统命名为 **UNiplexed Information and Computing Service** ，和Multiplexed informtion and Computing Service的意思相反，叫没路信息运算系统，缩写 **Unics->UNIX** 。这个时候是1970年，所以这⼀年被称为UNIX元年，所以计算机的 **时间戳** 是从1970年1⽉1⽇0点0分0秒开始的。Richard Stallman(⾃由软件之⽗)，所谓 **⾃由软件** ⾃由就是指：⾃由使⽤、⾃由学习和修改、⾃由分发、⾃由创建衍⽣版。 GUN的定义是⼀个递归缩写，就是GUN IS NOT UNIX。在Unix第五版的时候，Bell公开了Unix。伯克利⼤学根据公开出来的UNIX，加了⼀些新功能和修改，然后命名为BSD（Berkeley SoftwareDistribution伯克利分发版）由于Linux的版权是Bell实验室，有版权，很贵。⼀份4万美元。所以在1991年的时候，Linus Torvalds公布了⾃⼰写的Linux操作系统，让⼤家随便玩，或者帮助修改，绝不收费。
## 2.Linux的种类
### (1)Ubuntu
Ubuntu有着漂亮的⽤户界⾯，完善的包管理系统，强⼤的软件源⽀持.
### (2)CentOS
⾮常多的商业公司部署在⽣产环境上的服务器都是使⽤的CentOS系统,CentOS是从RHEL源代码编译的社区重新发布版。
### (3)Debian
。debian整个系统基础核⼼⾮常⼩，不仅稳定，⽽且占⽤硬盘空间⼩，占⽤内存⼩。
### (4)Centos和RedHat区别

## 3.Linux的安装
### (1)虚拟机的安装
windows系统上，最常⻅的虚拟机有virtual box和VM Ware。
### (2)Linux镜像的安装
### (3)开启CPU虚拟化
* 启动界面 F2、F8、F12
* VT-X、Virtual Technology

## 4.Linux流程
```bash
#连接网络
cd /etc
cd sysconfig/
cd netword-scripts/
ll
vi ifcfg-enp0s3
:w保存
:q退出
reboot 重启
# 下载wget包
# 在当前用户root中 ~
yum install wget
# 下载nodejs在Linux系统中 
wget https://nodejs.org/dist/v8.11.3/node-v8.11.3-linux-x64.tar.xz
# 目录下：node-v8.11.3-linux-x64.tar.xz 解压此压缩包
xz -d node-v8.11.3-linux-x64.tar.xz
# 变成了 node-v8.11.3-linux-x64.tar 再解压
tar -xf node-v8.11.3-linux-x64.tar
# 出现了node-v8.11.3-linux-x64文件夹
cd - 回退上一文件夹
# 创建超链接 这个月就可以在Linux系统下根目录直接使用node指令了 
ln -s ~/node-v8.11.3-linux-x64/bin/node   /usr/bin/node
ln -s ~/node-v8.11.3-linux-x64/bin/npm   /usr/bin/npm
```
### (1)根目录路径
* /usr(UNIX Software Resource)
* /var(variable)
* /bin 下文件都是可执行文件
* /boot 存放系统开机文件
* /dev 连接的设备的都是已文件的形式存放在其中
* /etc (配置文件的)
* /home 用户目录 每有一个用户都会多一个文件夹在其中，但是root管理员在根目录下
* /lib Linux函数库
* /media 存放一些可以删除的设备，如：U盘、光盘等
* /mnt 基本和media类似
* /opt 存放第三方软件目录的，比较常用
* /sbin 只允许系统管理员执行的文件
* /srv 存放用户主动生成的数据，可以对外访问
* /tmp 零时文件
* /proc 不在硬盘存储，在内存存储（关机就没有了）
* /sys 虚拟文件系统。存放和系统相关和内核相关的模块信息
* /lost+found 当文件系统发生错误的时候，会把丢失的文件碎片存放在这边 

![目录](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/vm2.png)

### (2)Linux常用指令
```bash
cd /  #根目录
cd ~  #当前用户的主文件夹， 
pwd   #查询当前谁的用户文件夹下
cd .. #返回上一文件夹，回退功能
cd ../bin #回到上一级目录下的bin文件下
ls  #文件下的列表
ls -l 简写ll #文件夹下详情目录
ls -al  #与ll不同，可以看到文件夹下隐藏文件，隐藏文件以 .开头
mkdir test  #创建test文件夹，主义必须在当前用户主文件夹下。cd ~
mkdir -p test1/test2  
#同时创建test1及子路经下的test2文件。必须加 -p，否则不能同时添加文件夹
rmdir test #删除路径test，注意test下没有子路经
rm -rf test1 #强力删除，连同test1下的所有文件
vi a.txt # 在test文件下创建/进入一个a.txt文件
i  #进入a.txt下之后按i可以进入编辑，按esc退出编辑
:wq #在按esc键之后，输入这个指令w保存，q退出，加起来退出并保存
:q! #不保存强制退出
# 在a.txt中编辑完成之后在test路径下查询a.txt中内容指令
tail a.txt  #显示文件最后10行
tail -f a.txt #动态显示文件，从最后10行开始
tail -200f a.txt #动态显示文件，从200行开始
cat a.txt |grep "xx" #查找xx内容
chmod 777 b.sh  #更改文件权限
```

### (3)文件权限
* 第一个字符：路径还是文件(d表示路径，-表示文件)
* 之后的分三组，每组三个字符
第一组：当前所属用户的权限
第二组：当前所属组的权限
第三组：其他用户权限
* r代表用户有权限读
* w代表用户有权限写
* x代表用户有权限执行
* rwx的相反就是：---
`mkdir -m 723 test1` 创建test1文件并手动设置权限。**723**每个数字代表一个组的权限；r->4,w->2,x->1; 如果既要读写又执行，就将数字相加->7,以此类推

![文件权限](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/vm1.png)

### (4)文件的创建、编辑和删除
| 按键       | 价格    |   
| :------:   | :-----:   | 
|h或[←]       |光标左移⼀个字符 |      
| j或[↓]        |光标下移⼀个字符       | 
|k或[↑]        |光标上移⼀个字符      |  
| l或[→]        |光标右移⼀个字符     |  
| 数字加⽅向       |光标向该⽅向移动数字个位置      |  
| [ctrl] + f       |向下翻⻚      |  
| [ctrl] + b      | 向上翻⻚    |  
| [ctrl] + d       | 向下移动半⻚      |  
|[ctrl] + u      | 向上移动半⻚     |  
| +        |光标移动到⾮空格符的下⼀⾏      |  
| -        | 光标移动到⾮空格符的上⼀⾏      |  
| n + [space]       | n为数字，光标向右移动n个字符的距离     |  
|0或[home]        |光标移动到⾏⾸      |  
|$或[end]      |光标移动到⾏尾     |  
| G     | 移动到这个⽂件的最后⼀⾏      |  
| nG        | n为数字，移动到⽂件第n⾏    |  
| gg       |移动到⽂件第⼀⾏     |  
| n + [enter]       | n为数字，光标向下移动n⾏    |  
| /word        | 向下查找word字符串     |  
| ?word       | 向上查找word字符串    |  
| dd         | 删除光标所在⾏    |  
| x,X       | x表示向后删除⼀个字符，X表示向前删除⼀个字符    |  
| u        | 撤销上⼀个操作     |  
| [crtl] + r        |重复上⼀个操作     |  

### (5)⽂件内容搜索
* tail
tail ⽂件名 显示⽂件最后10⾏
tail -f ⽂件名 动态显示⽂件，从最后10⾏开始
tail -200f 动态显示⽂件，从最后200⾏开始
* cat
cat ⽂件名 显示⽂件内容
* less
less ⽂件名 显示⽂件内容
b 向后翻⼀⻚
d 向后翻半⻚
-m 显示百分⽐
-N 显示⾏号
-e 到⽂件末尾后退出
Q退出less命令
* grep
(1)⽂件过滤
cat ⽂件名 |grep "xx" 搜索⽂件中带有xx的⾏
(2)⽬录过滤
ll |grep "xx" 搜索当前⽬录中带有xx的⽂件
(3)其他
ps aux |grep "xx" 搜索带有xx的进程
ifconfig |grep "xx"查看信息中带有xx的⾏
* chmod
(1)⽂件权限标识
⽂件前的⼀串字符
第⼀个字符“-”表示普通⽂件；这个位置还可能会出现“l”链接；“d”表示⽬录
第⼆三四个字符“rw-”表示当前所属⽤户的权限。 所以⽤数值表示为4+2=6，也就是110
第五六七个字符“rw-”表示当前所属组的权限。 所以⽤数值表示为4+2=6，也就是110
第⼋九⼗个字符“r--”表示其他⽤户权限。 所以⽤数值表示为2，也就是100
(2)chmod修改⽂件权限
chmod 777 ⽂件名 第⼀个字符不能修改，其余每三个字符⽤⼀个数字表示，按照⼆进制4，2，1求和表示

## 5.linux用户操作指令
```bash
ip addr #查看ip地址
useradd ck  #创建用户
cat /etc/passwd  #创建的用户都在这个文件夹中
ssh ck@192.168.171.88  #在其他虚拟机上访问ck用户
cat /etc/group  #查找用户组，当创建用户的时候会自动生成同名的用户组
groupadd animals  #创建用户组animals
useradd -G animals cat  #在animals用户组中创建cat用户，同时还会创建一个cat主要组
groupdel animals  #删除animals组
userdel cat  #删除cat用户
groups  #查看当前用户属于那个组
whoami  #也是查看当前用户属于哪个组
groups ck  #查看ck用户属于哪个组
useradd -G animals dog #在animals用户组中创建dog用户，同时还会创建一个dog主要组
usermod -g animals dog #讲god用户的主要组变成animals,本来的dog主要组变空了，此时可以删除dog组了
id horser  #查看用户信息，uid用户id，gid主要组的id，groups位于那些组中
su ck  #切换用户，从当前用户切换到ck用户
exit   #若当前在ck用户中，exit指令退出到root管理员用户
lscpu  #查看cpu信息
df     #查看各个磁盘信息，Use只使用率
df  -i  #查看磁盘缩影的空间
df  -h  #查看磁盘内容空间
ps aux  #系统都启用那些程序
ps aux |grep "node" #查看node有没有活动。如果只有一条当前搜索的活动列，就表示node没有进程
ps aux |grep "ssh"  #查看ssh的活动进程，有多条列表示尚在活动
top  #查看机器运行的资源占用情况
node test.js  #创建test.js ，通过node执行
```
## 6、linux指令`top`信息 查看机器运行的资源占用情况
![top](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/2.png)
## 7、指令df磁盘占用
![df](https://raw.githubusercontent.com/riceCk/riceBlog/master/images/3.png)