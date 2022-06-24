---
title: 技术面之六TS
categories: 2022前端面试梳理
tags: 面试题
date: 2022-04-25 09:48:00
---

## typeScript面试题相关

### 1.为什么要使用typeScript
> 增加静态类型，可以在开发编写脚本监测错误，使得代码质量更好，更健壮
> - 杜绝手误导致的变量名错误
> - 类型可以一定程度上充当文档

### 2.typescript中const和readonly的区别？枚举和常量枚举的区别？接口和类型别名的区别？
> - **const** 和 **readonly**：const可以防止变量值被修改，redonly可以防止变量的属性被修改
> - **接口**和**类型**：两者都是可以用来描述对象和函数的。与接口不用，类型别名可以用去其他类型，比如基本类型（原始值）、联合类型、元祖
> - **枚举** 和 **常量枚举**: 常量枚举只能使用常量枚举表达式，并且不用常规的枚举，他们在枚举斌编译阶段会被删除。常量枚举成员在使用的地方会被内联进来。之所以可以这么做

### 3.TypeSCript中interface可以Function / Array / Class(Indexable) 做声明嘛
```bash
/**可以**/
## 函数声明
interface Say {
    (name: string): viod;
}
let say: Say = (name: string):viod = > {}
## Array 声明
interface NumberArray {
    [index: number]: number
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
## class 声明
interface PersonalIntl {
    name: string
    sayHi: (name: string): string
}
```
### 4.TypeScript 如何设计Class的声明
```bash
# 在声明类的时候，一般类中都会包含，构造函数、对构造函数中的属性进行类型声明、类中的方法
class Greeter {
    greeting: string;
    constructor (message: string) {
        this.greeting = message;
    }
    greet(): stirng {
        return "Hello, " + this.greeting;
    }
}
```
### 5.TypeScript中**type**和**interface**区别
**相同点：**
* 1.都可以描述对象和函数
* 2.都允许扩展（extends）

**不同点：**
* 1.type可以声明基本类型，联合类型，元祖
* 2.type可以使用typeof获取实例的类型进行赋值
* 3.多个相同的interface声明可以自动合并

使用interface描述“数据结构”，使用type描述“类型关系”

### 6.对TypeScript类中成员的public、private、protected、readonly修饰符的理解
> **public**:成员都默认为publi，被次选定修饰符的成员可以被外部访问
> **private**:被此限定符修饰的成员是只可以被类的内部访问（子类不可以访问）
> **protected**:被限定符修饰的成员是只可以被类的内部以及子类访问
> **readonly**:关键字将属性设置为只读的。只读属性必须在声明时或构建函数里被初始化

