---
title: MySql数据库
categories: 了解nodejs前世今生
tags: 数据库
date: 2018-09-06 22:00:00
---
# 一、数据库基础
### 1、数据库
**数据库：**一个以某种有组织的方式存储的数据集合，保存有组织的数据的容器
### 2、表
**表：**某种特定类型数据的结构化清单
表是一种结构化文件，可用来存储某种特定类型的数据
数据库中每个表都有唯一的一个名字，用来标识自己，表名的唯一性取决于多个因素：如数据库名等结合（相同数据库不能出现名字相同的表，但不同数据库可以使用相同的表名）表具有一些特性，其定义了数据在表中如何存储，比如：存储什么类型的数据，数据如何分解，各部分信息如何命名等描述表的这组信息可以称为“模式”，其用来描述数据库中特定的表以及整个数据库（和其中表的关系）
**模式（schema）：**关于数据库和表的布局及特性的信息，一般指给予表的一些定义
### 3、列和数据类型
表由列组成，列中存储着表中某部分的信息
**列（column）：**表中的一个字段；所有表都有由一个或多个列组成的
分解数据：正确的将数据分解为多个列极为重要：例如省市县应该是独立的列，通过分解，才可能利用特定的列对数据进行排序和过滤，使得检索和访问更加方便（例如找出特定市县的相关数据等）
**数据类型（datatype）：**所容许的数据的类型：数据库中每个列都有相应的数据类型，其定义列可存储的数据种类（限制/容许该列中存储的数据）
**作用：**
* 限制可存储在列中的数据种类（例如防止在数值字段中录入字符值）
* 帮助正确的排序数据
* 对优化磁盘使用有重要作用

### 4、行
**行（row）：**表中的一个记录
表中的数据是按行存储的，保存的每个记录存储在自己的行内；如果将表想象为网格，网格中垂直的列为表列，水平行为表行
### 5、主键
**主键（primary key）：**一列或一组列，其值能够唯一区分表中的每一行；唯一标识表中每行的这个列（或这组列）称为主键
主键用来表示一个特定的行；没有主键，更新或删除表中特定行很困难，因为没有安全的方法保证操作只涉及相关的行
主键并不总是需要，但大多数情况下都应保证每个表具有一个主键，以便于数据操作和管理表中任何列都可以作为主键，只要满足以下2个条件：
* 任意两行都不具有相同的主键值；
* 每个行都必须具有一个主键值（主键列不允许NULL值）；

主键值规则:主键通常定义于表的一列上，但也可以一起使用多个列作为主键，这种情况下，上述2个条件必须应用到构成主键的所有列，所有列值的组合必须是唯一的（单个列可以不唯一）

设置主键的几个好习惯：
* 不更新主键列中的值；
* 不重用主键列的值；
* 不在主键中使用可能会更改的值（例如使用电话号码作为主键以标识某个人，当该人更改电话号码时，必须更改这个键）

## SQL简介
**SQL：**结构化查询语言（Structured Query Language）缩写，一种专门用来与数据库
通信的语言
设计SQL的目的是很好的完成一项任务，即提供一种从数据库中读写数据的简单有效的方法
**优点如下：**
* 几乎所有的DBMS都支持SQL，关系型数据库
* SQL简单易学，语句由描述性很强的英语单词构成，而且单词的数目并不多
* 一门强有力的语言，灵活使用其语言元素，可进行非常复杂和高级的数据库操作

## MySQL简介
MySQL是一种DBMS，即它是一种数据库软件
**MySQL优点：**
* 成本低，MySQL开放源代码，一般都可以免费使用甚至免费修改
* 性能好，MySQL执行效率很快
* 很多大型公司（包括一些全世界知名的互联网企业）使用MySQL来处理自己的重要数据
* 简安装使用非常容易

# 二、数据的检索和过滤

## 1、检索数据
```bash
select column from table
select * from actor
select t.actor_id, t.first_name from actor t
# distinct自动过滤重复的数据
select distinct first_name from actor
# 限制结果
select * from actor LIMIT 5 #查询前五的数据
select * from actor LIMIT 5,5 #查询从第六行开始的后五行数据
```
## 2、排序检索数据 
### 排序单个列：order by子句
select column from table order by column
```bash
# 根据名开始排序，默认正序 asc
select * from actor ORDER BY last_name
# 倒叙 desc
select * from actor ORDER BY last_name DESC
```
### 按多个列排序
select column1，column2，column3 from table order by column1， column2
```bash
select * from actor ORDER BY last_name DESC, first_name
```
### 检索最高或最低值
limit 1告诉MySQL只返回一行的数据 
```bash
# 按actor_id检索最高
select * from actor ORDER BY actor_id DESC LIMIT 1
# 按actor_id检索最低
select * from actor ORDER BY actor_id (asc) LIMIT 1
```

## 3、过滤数据 
### where子句 
使用order by和where子句，order by位于where之后（否则会出错） 
```bash
# 条件查询，查询名字为ed的列，并且以倒叙排
select * from actor where first_name = 'ED' ORDER BY last_name DESC
```
### where子句操作符 
=：等于      <>：不等于       <:小于     
<=:小于等于   >:大于          >=:大于等于between：在指定的两个值之间 
```bash
# 筛选 id 150-180的数据
select * from actor where actor_id > 150 AND actor_id < 188 ORDER BY actor_id
select * from actor WHERE actor_id BETWEEN 150 AND 180 ORDER BY actor_id 
```
### 不匹配检查 
select column from table where column <> N； select column from table where column != N； 

### 范围值检查 
select column from table where column between X and Y；
### 空值检查 
select column from table where column1 is null

## 4、使用操作符过滤数据
**操作符（operator）：**用来联结或改变where子句中的子句的关键字。也称为逻辑操作符 
### and操作符 
select column1， column2，column3 from table where column1 = X and  column <=Y；
### or操作符 
select column1， column2 from table where column1 = X or column  <=Y
### 计算次序 
select column1，column2 from table where column1 =X or column1 = Y  and column2 >= N
从table表中过滤出column2为N（含）以上且column1=X或Y的的所 有行`（实际中是先计算column1 = Y and column2 >= N然后计算column1 =X）` where可包含任意数目的and和or操作符；允许两者结合以进行复杂和高级的过滤（需要说 明的是：**and在计算次序中优先级高于or**） 


### in操作符 
in操作符用来 指定匹配值的清单的关键字，功能和or相当
```bash
# 筛选出id为1和4的数据
select * from actor WHERE actor_id in (1, 4)
```

### not操作符 
not操作符用来否定后跟条件的关键字
```bash
select * from actor WHERE actor_id not in (1, 4, 5)
```

## 5、使用通配符过滤数据
**通配符（wildcard）：**用来匹配值的一部分的特殊字符，利用通配符可以创建比较特定数 据的搜索模式（实际上是SQL的where子句中带有特殊含义的字符） 
**搜索模式（search pattern）：**由字面值、通配符或两者组合构成的搜索条件 为在搜索子句中使用通配符，必须使用like操作符；like指示MySQL后跟的搜索模式利用 通配符匹配而不是直接相等匹配进行比较
### 百分号（%）通配符
select column1， column2 from table where column1 like ‘test%’ 
表示任意以test起头的词；%告诉MySQL接受test之后的任意字符，不管它有多少字符,test%：所有以test起头的值,%test%：匹配任何包含test的值,te%st：所有 以te开头以st结尾的值  
```bash
# 查找名字带da的数据，模糊查询
SELECT * FROM actor WHERE first_name LIKE '%da%'
```

### 下划线（_）通配符
下划线通配符作用和%一样，但下划线通配符只匹配单个字符而不是多个字符（与%能匹配0 个字符不一样，总是匹配一个字符） 
```bash
select * from actor where first_name like 'da_'
```
### 使用通配符的技巧 
* 不要过度使用通配符（如果其他操作符能达到同样目的，应使用其他操作符） 
* 在确实需要使用通配符时，除非绝对有必要，否则不要把通配符用在搜索模式开始处（这样做是最慢的） 
* 注意通配符所在的位置


# 三、插入和更新
## 1、insert
**insert：**用来插入（或添加）行到数据库中，常见方式有以下几种： 
* 插入完整的行； 
* 插入行的一部分； 
* 插入多行； 
* 插入某些查询的结果

### 插入完整的行
```bash
insert into usertable(user_id,      
    user_name,      
    user_year，      
    user_birthday,      
    user_address,      
    user_email) 
values(null,      
    'zhangsan',      
    '25',      
    '1992­05­20',      
    null,      
    null);
```
```bash
insert into actor values (201, 'ck', 'kk', '2018-09-11 17:10:11')
insert into 
    actor(actor_id, first_name, last_name, last_update) 
    values (202, 'ckk', 'kkk', '2018-09-11 17:20:11')
```
### 插入多行
```bash
insert into actor(actor_id, first_name, last_name, last_update) 
    values (202, 'ckk', 'kkk', '2018-09-11 17:20:11'),
           (203, 'ck', 'kk', '2018-09-11 17:20:11'),
           (204, 'xkk', 'k', '2018-09-11 17:20:11'),
```
## 2、update更新数据 
### update语句由三部分构成： 
* 要更新的表； 
* 列名和它们的新值； 
* 确定要更新行的过滤条件； 

例如：update usertable set user_email = '3033310371@qq.com' where user_id = '10086'
```bash
update actor set first_name = 'xiaoming' where actor_id = 202
```
**ps：**如果用update更新多个行，且更新中一行或多行出现错误，则update操作将被取消 （错误发生前执行的动作将被回滚到最初状态。即已更新的被恢复原来的值）；如果希望出错误，更新继续执行，可以使用ignore关键字：
update ignore  tablebase......

## 3、delete删除数据
如果想删除表中所有行，可使用`truncate table`语句，它的执行效率比delete更快 （truncate实际上是删除原来表并重新新建一个表，而不是逐行删除表中数据）。 
delete from usertable where user_id = '10086'; 
```bash
delete from actor where actor_id = 202
truncate table actor
```


# 四、汇总和分组
## 1、汇总数据
SQL汇聚函数,ps:as(XXX) 自定义列名

函数|说明
:----:|:----: 
AVG()|返回某列的平均数
COUNT()|返回某列的行数
MAX() | 返回某列的最大值 
MIN()| 返回某列的最小值
SUM()| 返回某列值之和

### avg()函数
avg（）通过对表中行数计数并计算特定列值之和，求得该列的平均值
avg（）可用来返 回所有列平均值
```bash
SELECT avg(rental_rate) AS rental_rate FROM film
# rental_rate => 2.98
```
可用来返回特定列的平均值
```bash
SELECT
    avg(rental_rate) 
AS 
    rental_rate 
FROM 
    film 
where 
    rental_duration = 6
```
### count()函数
count()函数进行计数，可利用count()确定表中行的数目或符合特定条件的行的数目
```bash
# 对rental_rate所有行进行合计
SELECT count(*) AS rental_rate FROM film 
# 对有rental_duration有值的rental_rate进行合计，没值的忽略
SELECT 
    count(rental_duration) 
AS 
    rental_rate 
FROM 
    film 
```
### max()函数 
max（）返回指定列中的大值，max（）要求指定列名
```bash
SELECT MAX(rental_rate) AS rental_rate FROM film
```

### sum()
函数 sum()函数用来返回指定列值的和（总计）
```bash
SELECT SUM(rental_duration) AS rental_duration合计 FROM film
```
### distinct与聚集函数
```bash
# 计数：计数条件为rental_duration=6的ranting不重复的数据列
SELECT 
    COUNT(DISTINCT rating) 
as rating 
from 
    film 
WHERE rental_duration = 6
```

### 组合聚集函数 
```bash
select count(*) as 计数,
    min(rental_rate) as rete列最小,
    max(rental_duration) as duration列最大,
    avg(replacement_cost) as cost列平均
from film
```
案例
```bash
SELECT
    t2.first_name,
    t2.last_name,
    MAX(t1.amount) AS 最高花费,
    MIN(t1.amount) AS 最低花费,
    SUM(t1.amount) AS 总花费,
    AVG(t1.amount) AS 平均花费,
    COUNT(t2.first_name) AS 租借次数，
FROM
    payment t1
JOIN customer t2 ON t1.customer_id = t2.customer_id
JOIN rental t3 ON t1.rental_id = t3.rental_id
JOIN inventory t4 ON t3.inventory_id = t4.inventory_id
WHERE
    t4.store_id = 1
GROUP BY
    t2.first_name,
    t2.last_name,
    t4.store_id
```
first_name|last_name|最高花费|最低花费|总花费|平均花费|租借次数
:----:|:----:|:----:|:----:|:----:|:----:|:----:
AARON|SELBY|8.99|0.99|49.89|4.535455|11
ADAM|GOOCH|8.99|0.99|47.89|4.353636|11
ADRIAN|CLARY|5.99|0.99|33.89|3.080909|11
AGNES|BISHOP|8.99|2.99|49.89|4.535455|11
ALAN|KAHN|8.99|2.99|68.85|4.59|15
ALBERT|CROUSE|6.99|0.99|34.91|3.878889|9
ALBERTO|HENNING|6.99|0.99|23.94|3.99|6
ALEX|GRESHAM|9.99|0.99|64.86|4.632857|14
ALEXANDER|FENNELL|6.99|0.99|54.84|3.4275|16
ALFRED|CASILLAS|9.99|0.99|37.90|3.79|10

## 2、分组数据 
### group by创建分组
进行分组整合，下例则是整合rental_duration进行数据相同的整合，同时进行对整合的数据进行计数
```bash
select 
    rental_duration,count(*) 
as 
    num_prods 
from 
    film 
group by 
    rental_duration
```
rental_duration|num_prods
:----:|:----: 
3|203
4|203
5| 191 
6| 212
7| 191
* group by子句可以包含任意数目的列（使得对分组进行嵌套，为数据分组提供更细致的 控制）
* 如果在group by子句中嵌套分组，数据将在后规定的分组上进行汇总，即：建立分组 时，指定的所有列都一起计算（所以不能从个别列取回数据）
* group by子句中列出的每个列都必须是检索列或有效的表达式（但不能是聚集函数）， 如果在select中使用表达式，则必须在group by子句中指定相同的表达式（不能使用别 名）
* 除了聚集计算语句外，select中每个列都必须在group by子句中给出
* 如果分组列中具有null值，则null将作为一个分组返回（如果列中有多行null值，他 们将分为一组）
* group by子句必须出现在where子句之后，order by子句之前

PS：使用with rollup关键字，可以得到每个分组以及每个分组汇总级别（针对每个分组）的值。 
### having过滤分组
where子句都可以用having代替，区别在于where过滤行，having过滤分组；having支持所有的where操作符
```bash
select
    rental_duration,count(*) 
as 
    计数
from 
    film 
group by 
    rental_duration
having count(*) >= 200
```
where在数据分组前进行过滤，having在数据分组后进行过滤；where排除的行不包括在 分组中（这可能会改变计算值，从而影响having子句中基于这些值过滤掉的分组）
having和where可以同时使用
```bash
select 
    rental_duration,count(*) 
as 
    计数
from 
    film 
WHERE
    rental_duration > 4
group by 
    rental_duration
having count(*) > 200
```
### 分组和排序 
group by和order by的区别：
order by|group by
:----:|:----: 
排列产生的输出|分组行，但输出可能不是分组的排序
任意列都可以用（甚至非选择的列也可以使用）|只可能使用选择列或表达式列，而且必须使用每个选择列表达式
不一定需要|如果与聚集函数一起使用列（或表达式），则必须使用
order by的重要性：一般使用group by子句时，应该也给出order by子句，这是保证 数据正确性的唯一方法（千万不要依赖group by排序数据

### select子句顺序
子句|说明|是否必须使用
:----:|:----:|:----:
select|要返回的列或表达式|是否必须使用
from|从中检索数据的表|仅在从表选择数据时使用
where|行级过滤|否
group by|分组说明|仅在按组计算聚集时使用
having|组级过滤|否
order by|输出排列顺序|否
limit|要检索的行数|否

# 五、表连接
## 1、连接表基础知识
### 关系表
把信息分解成多个表，一类数据一个表，各表通过某些常用值（即关系设计中的关系 （relational））互相关联； 关系型数据库中常用关系为三种：
* 一对一 用户 身份证
* 一对多（多对一）
* 多对多 

### 外键
外键为某个表中的一列，它包含另一个表的主键值，定义了 两个表之间的关系 
### 连接表的优点：
* 数据信息不重复，从而不浪费时间和空间 
* 如果某个数据信息变动，可以只更新该表中的某个记录，相关表数据不用变更 
* 数据无重复，可以更有效的存储和方便的处理，伸缩性强（能够适应不断增加的工作量而 不失败，设计良好的数据库或者应用程序称为可伸缩性好（scale well））

PS：连接是一种机制，用来在一条select语句中关联表，因此称之为连接（连接在运行时 关联表中正确的行） 

## 2、创建连接
连接address, city表
```bash
select 
    address.district, city.city, city.last_update
from 
    address, city
where 
    address.city_id = city.country_id
order by 
    district, city
```
连接 **address**和 **city**两个表，以id相同为公共列，district和city为排序参数
**PS：**where子句的重要性 在一条select语句中连接几个表时，相应的关系是在运行中构造的；在连接两个甚至多个 表时，实际上是将第一个表中的每一行与第二个表中的每一行配对，where子句作为过滤条
件，它只包含匹配的给定条件的行。 没有where子句，第一个表中每个行将与第二个表中的每个行匹配，而不管逻辑上是否可以 配在一起
### 笛卡尔积
由没有连接条件的表关系返回的结果称为笛卡尔积（检索出的行的数目将是第一个表中的行数乘以第二个表中的行数）； 例如：
```bash
select 
    A_name,B_name,B_mobile     
from 
    A, B     
order
    by A_name, B_name
```
**PS:**应保证所有连接都有where子句，否则MySQL将返回比想要的数据多得多的数据，还应 保证where子句的正确性。
### 内部连接 
等值连接（equijoin）：基于两个表之间的相等测试，也称为内部连接
```bash
select 
    A_name,B_name,B_mobile     
from 
    A innter join B     
on 
    A_name = B_name; 
```
这条SQL语句中，select语句与之前的相同，相较于上面的from，这里使用了inner join指定（当使用inner join时，连接条件用on子句而不是where子句，在这里on等同于where） 
```bash
SELECT
    t1.first_name,
    t1.last_name,
    t3.title,
    t3.description
FROM
    actor t1
INNER JOIN film_actor t2 ON t1.actor_id = t2.actor_id
INNER JOIN film t3 ON t3.film_id = t2.film_id
```
### 连接多个表 
SQL对一条select语句中可以连接的表的数目没有限制，创建规则也基本相同（首先列出所有表，然后定义表之间的关系） 
**PS：**MySQL在运行时关联指定的每个表以处理连接，这样很耗费资源，因此使用中应注意，不要连接不必要的表。

## 3、创建高级连接
### 使用表别名
别名除了允许用于列名和计算字段外，SQL还允许给表名起列名，好处在于： 
* 缩短SQL语句
* 允许在单条select语句中多次使用相同的

```bash
select 
    a.district, b.city, b.last_update
from 
    address as a , city as b
where a.city_id = b.country_id
```
### 自连接
自连接通常作为外部语句用来替代从相同表中检索数据时使用的子查询语句
```bash
select 
    p1.user_id, p1.user_name 
from
    usertable as p1, usertable as p2
where 
    p1.user_id = p2.user_id
and 
    p2.user_id = '10086'
```
这条SQL语句中，usertable表第一次出现为别名p1，第二次为别名p2，在select语句中明确使用p1前缀给出所需列名，否则MySQL将返回错误，因为其无法正确区分想要的具体为哪一列
### 外部连接 
用来检索包含了在相关表中没有关联行的行，这种类型的连接称为外部连接
**外部连接的类型：**
`left join`and`right join`
分为左外部连接和右外部连接，唯一差别是关联表的顺序不同，左外部连接可以通过颠倒 from或where子句中的表顺序转换为右外部连接，两种类型可以互换使用（聚集函数也可 和连接一起使用）。
左连接
**PS：**连接使用要点 
* 注意使用的连接类型 
* 保证使用正确的连接条件，否则将返回不正确的数据 
* 应该总是提供连接条件，否则会得到笛卡尔积
* 同一个连接下可包含多个表，甚至对每个连接采用不同的连接类

# 六、实战
```bash
show databases ;   # 打开查看数据库
use my_blog;         # 连接mysql库
show tables ;      # 打开显示mysql库里的表
desc db;           # 查看db表字段
show create table db;  # 查看创建db表的sql语句
select * from db;      # 查看表
create database school; # 创建库
show databases;
alter database school character set utf8;  # 设置school库为utf8格式
alter table student default character set utf8; # 设置表为utf8格式
alter table student convert to character set utf8;  # 设置表字段为utf8格式

delete from `blog` where `id` = 16;

insert into blog (`title`, `content`, `tags`, `views`, `ctime`, `utime`)
      value('这是标题', '<h1>这是内容标题</h1><h2>这是内容标签</h2><p>这是内容</p>', '这是标签', 0, '2019-02-19 09:25:31', '2019-02-19 09:25:31');      # 增加内容
insert into every_day(`ctime`, `content`) value ('2019-02-19 09:25:31', '21312312');
select * from blog where tags='123'  order by id desc limit 0, 5;
update student set age = 18, class = 3 where id = 2; # 修改
select content from every_day;          # 查看
select count(*) as count1 from blog;  # 统计

select count(1) as count from blog where tags = '123';

SELECT * FROM blog GROUP BY ctime;
update blog set views = views + 1 where id = 13;
select * from blog order by views desc limit 7;
select * from blog where tags = '';

select * from blog where id = (select id,title from blog where id < 22 order by id desc limit 1);
select * from blog where id = (select id,title from blog where id > 22 order by id asc limit 1);

select sum(age) from student;               # 求和
select sum(age) / count(1) as '平均值' from student;  # 计算平均数
select class, count(1) from student group by class;   # 分组
select * from student limit 2,2; # 分页偏移量
select * from student order by id desc; # 倒叙
delete from student where name="小猫";
select * from student limit 6,3;
select stu_num from student;
select ctime, content from every_day order by id desc limit 1;

# 设置其他服务连接
show databases;  # 打开查看数据库
use mysql;   # 连接mysql
select host, user from user; # 查询user的用户名
update user set host="%" where host = "localhost" and user="root";
```