---
title: 1_Flutter必备Dart基础
categories: flutter
tags: flutter
date: 2021-07-27 09:47:00
---

## Mixins
* Mixins类似于多继承，是在多类继承中重用一个类代码的方式

```bash
void main () {
  var d = new D();
  # 多继承的方式
}
class A {
  void a () {
    print("A.a()...");
  }
}

class B {
  void b () {
    print("B.b()...");
  }
}

class C {
  void c () {
    print("C.c()...");
  }
}

class D extends A with B,C {
  
}
```

* 作为Mixin的类不能有显示声明构造方法
* 作为Mixin的类只能继承自Object
* 使用关键字with连接一个或多个mixin

```bash
# 抽象类 引擎
abstract class Engine {
  void work();
}

# 接口 烧油
class OilEngine implements Engine {
  @override
  void work () {
    print("work width oil...");
  }
}

# 电动 引擎
class ElectricEngine implements Engine {
  @override
  void work () {
    print("work width Electric...");
  }
}
 # 轮胎
class Type {
  late String name;
  void run () {}
}
#简写
class Car = Type with ElectricEngine;
# 完整
class Car2 extends Type with OilEngine {}
```

## 操作符覆写
* 腹泻操作符需要在类中定义

```bash
返回类型 operator 操作符 (参数1， 参数2, ...) {
  实体类
  return 返回值
}
```
* 如果覆写 `==`，还需要覆写对象的`hashCode`getter方法

|    |   |
| -- | --|
| <  | + | I  | []  |
| >  | / | ^  | []= |
| <=  | ~/ | &  | ~ |
| >=  | * | <<  | == |
| -  | % | >>  |  |

```bash
void main () {
  var person1 = Person(19);
  var person2 = Person(20);
  print(person1 > person2)
}


class Person {
  late int age;
  Person(this.age);

  bool operator > (Person person) {
    return age > person.age;
  }

  int operator [] (String str) {
    if ('age' == str) {
      return age;
    }
    return 0;
  }
}
```

## 枚举
* 枚举是一种有穷序列集的数据类型
* 使用关键字`enum`定义一个枚举
* 常用于代替常量，控制语句等

```bash
void main () {
  var currentSeason = Season.autumn;

  switch (currentSeason) {
    case Season.spring:
      print('1-3月');
      break;
    case Season.summer:
      print('4-6月');
      break;
    case Season.autumn:
      print('7-9月');
      break;
    case Season.winter:
      print('10-12月');
      break;
  }
}

enum Season {
  spring,
  summer,
  autumn,
  winter
}
```

### Dart枚举特性
* index从0开始的，依次累加
* 不能指定原始值
* 不能添加方法

## 泛型
* Dart中类型是可选的，可使用泛型限定类型
* 