---
title: 2_ Flutter必备Dart基础
categories: flutter
tags: flutter
date: 2021-07-28 09:47:00
---

# Flutter之Dart概述
JIT&AOT
* JIT：即时编译，开发期间，更快编译，更快的重载
* AOT：事前编译，release期间，更快更流畅

# Dart的常用数据类型
## 1.var
类似于 JavaScript 中的var，它可以接收任何类型的变量，但最大的不同是 Dart 中 var 变量一旦赋值，类型便会确定，则不能再改变其类型，
```bash
var t = "hi world";
# 下面代码在dart中会报错，因为变量t的类型已经确定为String，
# 类型一旦确定后则不能再更改其类型。
t = 1000; # 报错
```
## 2.dynamic 和 Object 动态数据类型
**Object**是`Dart`所有对象的根基类，也就是说Dart中的所有类型都是**Object**的子类（包括Function和Null）
```bash
dynamic t;
Object x;
t = "hi world";
x = 'Hello Object';
# 下面代码没有问题
t = 1000;
x = 1000;
```
## 3.final 和 const
如果您从未打算更改一个变量，一个`final`变量只能被设置一次
```bash
# 可以省略String这个类型声明
final str = "hi world";
# final String str = "hi world"; 
const str1 = "hi world";
# const String str1 = "hi world";
```

## 4.num 和 int，double数字类型
**num**是数字类型的父类，**int**只能是整数，**double**双精度
```bash
num num1 = -1.8;
num num2 = 2; # 是数字类型的父类
int int1 = 3; # 只能是整数
double d1 = 1.74; # 双精度
print('num:$num1 num:$num2 int:$int1 double:$d1');
print(num1.toInt()); # 装换成整数int
print(num1.toDouble()); # 转换成double
```

## 5.String 字符串类型
支持`单引号`、`双引号`类型
* substring 截取字符串，左闭右开原则
* indexOf 和js语法一致，是否有，返回：-1或者坐标
* startsWith 判断起始字符串，返回：true或false
* replaceAll 替换字符串，第一个参数原始，第二个参数替换
* contains 判断目标参数是否存在，返回：true,false
* split 字符串转成数组类型，和js类似

```bash
String str1 = '字符串', str2 = "双引号";
String str3 = 'str1:$str1 str2:$str2';
String str4 = 'str1:' + str1 + 'str2:' + str2;
String str6 = '''hello
    world'''; 
print(str3);
print(str4);
String a = 1;
String b = 2;
print("a + b = ${a + b}");
print("a = $a");
# 常用的方法
String str5 = 'hello world';
print(str5.substring(1, 4)); # ell; 截取字符串，左闭右开原则
print(str5.indexOf('lo')); # 3
print(str5.startsWith('hello')); # 判断起始字符串
print(str5.replaceAll("world", 'string')); # hello string, 替换字符串
print(str5.contains('111'));  # 字符串是否存在
print(str5.split(' ')); # [hello, world] 字符串转成数组类型，和js类似
```

## 6.布尔类型
布尔数据类型，Dart是强bool类型检查，只有bool类型的值是true，才被人认为true
```bash
bool success = true, fail = false;
print('bool:$success bool:$fail');
print(success || fail);
print(success && fail);
```

## 7.List集合
和type javasrcipt 类型差不多，也支持泛型定义
* add 添加元素
* addAll 代器，类似js，[...list]
* generate 
* 集合的遍历，for(), for(var i in list), list.forEach
* remove 删除参数的某值
* insert 插入某值
* sublist 截取一段
* indexOf 和js类似
* sort 排序

```bash
# 集合初始化的方式
List list = [1, 2, 3, '集合']; # 集合初始化添加元素
print(list);
# 集合的泛型定义
List<int>list2 = [1, 2, 3]; # 只能定义int类型的集合
List list3 = [];
list3.add('list3'); # 通过add方法添加元素
list3.addAll(list); # 迭代器，类似js[...list]
List list4 = List.generate(3, (index) => index * 2); # [0, 2, 4]
# 集合遍历的方式
for (int i = 0; i < list.length; i++) {
  print(list[i]);
}
for (var k in list) {
  print(k);
}
list.forEach((el) {
  print(el);
});
list.remove(5); # 删除参数的某值
list.insert(1, '122'); # [1, 122, 2, 3, 集合]
list.sublist(2 ,4); # [2, 3]
list.indexOf(3); # 3
```

## 8.Map 对象
* forEach 遍历
* containsKey是否存在某键
* containsValue 是否存在某值
* keys 
* isEmpty 是否为空，true为空，false不为空
* remove 删除某个值

```bash
Map names = {
  "xiaoming": "小明",
  "xiaohong": "小红",
};
Map ages = {};
ages['xiaoming'] = 18;
ages['xiaohong'] = 15;
ages.forEach((key, value) {
  print('key:$key, value:$value');
});
Map ages2 =  ages.map((key, value){
  return MapEntry(key, value + 1); # MapEntry处理key，value数值
});
print(ages.keys); #  (xiaoming, xiaohong)
for (var key in ages.keys) {
  print('$key ${ages[key]}');
}
print(ages.keys); # (xiaoming, xiaohong)
print(ages.values); # (18, 15)
ages.remove('xiaoming'); # {xiaohong: 15}
ages.containsKey('xiao'); # 是否存在键
print(map.containsValue("Dart")); # 是否存在值
var map = {"first": "Dart", "second": "Java", "third": "Python"};
print(map.length);
print(map.isEmpty); # 是否为空，true为空，false不为空
```

