---
title: mongoDB简介
categories: 了解nodejs前世今生
tags: 数据库
date: 2018-09-13 21:28:00
---
# 常用命令
## 1、数据库命令
**db**：显示当前数据库对象或集合
**show dbs**：显示所有数据的列表
**use**：连接到一个指定的数据库，如果数据库不存在，则创建数据库
**db.dropDatabase()**:删除当前数据库
## 2、集合命令
**db.createCollection(name, options)**:
* name: 要创建的集合名称 
* options: 可选参数, 指定有关内存大小及索引的选项 
  capped  布尔（可选）如果为true，则创建固定集合固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。当该值为true 时，必须指定size参数。 
  autoIndexId布尔（可选）如为 true，自动在 _id 字段创建索引。默认为 false。
  size数值（可选）为固定集合指定一个最大值（以字节计）。如果capped为 true，也需要指定该字段。
  max数值（可选）指定固定集合中文档数量 

**show collections**查看已有集合
**db.collection.drop()**删除集合 举例：db.mycollection1.drop() 删除mycollection1 删除 成功返回true

## 3、文档命令
### （1）文档插入命令
单行插入：**db.collection_name.insert(document)**
多行插入：**db.collection_name.insertMany([{"title":"aaa","des":"aaa","order":1}, {"title":"bbb","des":"bbb","order":2},{"title":"ccc","des":"ccc","order":3}, {"title":"aaa","des":"其实我是ddd","order":4}])**

```bash
# 单行插入：mycollection文档
db.mycollection.insert({"title": "aaa","des": "bbb"})
# 查看已有集合
show collections # -> mycollection

# 多行插入
db.collection_name.insertMany([
  {"title":"aaa","des":"aaa","order":1}, {"title":"bbb","des":"bbb","order":2},{"title":"ccc","des":"ccc","order":3}, {"title":"aaa","des":"其实我是ddd","order":4}])
```

### （2）文档查询命令 
**db.collection_name.find().pretty()**
pretty()是格式化输出

**条件查询**

条件|db指令|sql指令
:---:|:---------------------------------:|:-------------------------------------:
等于| db.col.find({"title":"aaa"}).pretty()| where title = 'aaa'
小于| db.col.find({"order":{$lt:2}}).pretty() |where order< 2
小于或等于| db.col.find({"order": {$lte:3}}).pretty() |where order<= 3
大于| db.col.find({"order": {$gt:1}}).pretty()|where order> 1
大于或等于| db.col.find({"order": {$gte:2}}).pretty() | where order>= 2
不等于| db.col.find({"order": {$ne:1}}).pretty() | where order <> 1

**AND条件**
and条件即查询条件时传入多个属性 
实例:
```bash
db.col.find({"title":"aaa","des":"bbb"})
```
**OR 条件**
```bash
db.mycollection.find({$or:[{"title":"aaa"},{"title":"ccc"}]}).pretty()
```

**正则条件**
```bash
db.mycollection.find({"title":{$regex:/aa/i}})
```

**指定字段查询**
1、包含字段inclusion模式
db.collection.find(查询条件, {title: 1, des: 1}) 
```bash
db.collection_name.find(
    {
      $or:[{
        "title":"aaa"
        },{
          "des":"ddd"
        }]
    },{
      "title":1,
      "des":1
    }
  ).pretty()
```
2、不包含 exclusion模式 
db.collection.find(查询条件, {title: 0})

**Limit()方法**
查询前几条数据
```bash
db.collection_name.find().limit(1)
```
**skip() 方法**
从第几条开始查或查到第几条
```bash
# 返回的从第二条数据开始
db.collection_name.find().skip(2)
# 返回的第二条数据
db.collection_name.find().limit(1).skip(1)
```

3、文档删除命令
db.collection.remove()  
**注意:**remove方法需要传递参数，如果想要全部删除可以用db.col.remove({})
```bash
db.mycollection.remove({})
```

4、文档更新方法
**update是按字段更新**
db.col.update({'title':'aaa'},{$set:{'title':'MongoDB'}})
```bash
db.collection_name.update(
  {
    "title":"bbb"
  },{
    $set:{"title":"abc"}
  }
)
```
**save 覆盖对象的更新**
save为符合条件的文档对象进行更新，如果没有符合的ID，则插入一个
```bash
db.collection_name.save(
  {
    "_id":ObjectId("5b9a696f1c73bf260a6ad89f"),
    "title":"ddd"
  }
)
```
**排序方法** 
sort(1)为正序，sort(-1)为倒序
```bash
db.collection_name.find().sort({"order":1})
```

6、聚合函数
**count() 计数**
```bash
db.collection_name.count()
db.collection_name.count({"title":"aaa"})
```

**aggregate**求order和的实例
```bash
db.collection_name.aggregate(
  {
    $group:{
      _id:"title",
      sumOrder:{$sum:"$order"}
    }
  }
)
```
* $avg  计算平均值 
* $min  获取集合中所有文档对应值得最小值。 
* $max  获取集合中所有文档对应值得最大值。