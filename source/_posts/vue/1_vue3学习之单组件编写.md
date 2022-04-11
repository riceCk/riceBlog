---
title: 1_vue3学习之单组件编写
categories: Vue
tags: vue3
date: 2022-01-06 09:47:00
---

# 1.响应式API之ref
**ref**是最常用的一个响应式API，它可以用来定义所有类型的数据

### 类型声明
```bash
const msg = ref<string>('hello world!')
const phoneNumber = ref<number | string>(12312312312);
```
### 基本类型
```bash
# 字符串
const msg = ref=<string>('hello world!')
# 字符串或者数字
const phoneNumber = ref<number | string>(12312312312);
# 数值
const count = ref<number>(1);
# 布尔值
const isVip = ref<boolean>(false);
```


### 引用类型
```bash
# 泛型
interface Member {
  id: number,
  name: string
}
# 对象类型
const userInfo = ref<Member>({
  id: 1,
  name: 'tom'
})
# 数字数组
const uids = ref<number[]>([1, 2, 3, 4])
# 字符串数组
const names = ref<string[]>(['Tom', 'Petter', 'Andy'])
# 泛型对象数组
const memberList = ref<Member[]>([
  {
    id: 1,
    name: 'tom',
  }
])
```

### 变量读取
```bash
# 读取一个字符串
const msg = ref<string>('Hello World!');
console.log('msg的值', msg.value);

# 读取一个数组
const uids = ref<number[]>([ 1, 2, 3 ]);
console.log('第二个uid', uids.value[1]);
```

# 2.响应式API之reactive
**reactive**是继**ref**之后最常用的一个响应式API了，相当于**ref**,它的局限性在于只适合对象、数组
**特别注意：**
不要对通过`reactive`定义的对象进行解构，解构后得到的变量会失去响应性。

### 类型声明与定义
```bash
interface Member {
  id: number,
  name: string
}
# 声明对象的格式
const userInfo: Member = reactive({
  id: 1,
  name: 'tom'
})
# 对象数组
const uids: number[] = [1, 2, 3];
# 定义一个成员对象数组
const userList: Member[] = reactive([
  {
    id: 1,
    name: 'tom'
  }
])
```
### 变量的读取和赋值
```bash
console.log(userInfo.name);
userInfo.name = 'Petter';
```
# 3.响应式API之toRef与toRefs
### 各自的作用
两个api的拼写非常接近，顾名思义，一个是只转换一个字段，一个是转换所有字段。
| API | 作用|
|:---:|:---:|
|toRef|创建一个新的ref变量，转换reactive对象的某个字段为ref变量|
|toRef|创建一个新的对象，它的每个字段都是reactive对象各个字段的ref变量|

```bash
interface Member {
  id: number,
  name: string,
}
const userInfo: Member = reactive({
  id: 1,
  name: 'Petter'
})

const name = toRef(userInfo, 'name')
const userInfoRefs = toRefs(userInfo)
```

### 在业务中的具体运用
```bash
export default defineComponent({
  setup () {
    # 定义一个reactive对象
    const userInfo = reactive({
      id: 1,
      name: 'Petter',
      age: 18,
      gender: 'male'
    })

    # 定义一个新的对象，它本身不具备响应性，但是它的字段全部是ref变量
    const userInfoRefs = toRefs(userInfo);

    # 2s后更新userInfo
    setTimeout( () => {
      userInfo.id = 2;
      userInfo.name = 'Tom';
      userInfo.age = 20;
    }, 2000);

    # 在这里结构toRefs对象才能继续保持响应式
    return {
      ...userInfoRefs
    }
  }
})
```
```bash
<template>
  <ul class="user-info">

    <li class="item">
      <span class="key">ID:</span>
      <span class="value">{{ id }}</span>
    </li>

    <li class="item">
      <span class="key">name:</span>
      <span class="value">{{ name }}</span>
    </li>

    <li class="item">
      <span class="key">age:</span>
      <span class="value">{{ age }}</span>
    </li>

    <li class="item">
      <span class="key">gender:</span>
      <span class="value">{{ gender }}</span>
    </li>

  </ul>
</template>
```

# 4.函数的定义和使用
```bash
<template>
  <p>{{ msg }}</p>

  <!-- 在这里点击执行return出来的方法 -->
  <button @click="changeMsg">修改MSG</button>
  <!-- 在这里点击执行return出来的方法 -->
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from 'vue'

export default defineComponent({
  setup () {
    const msg = ref<string>('Hello World!');

    # 这个要暴露给模板使用，必须return才可以使用
    function changeMsg () {
      msg.value = 'Hi World!';
    }

    # 这个要在页面载入时执行，无需return出去
    const init = () => {
      console.log('init');
    }

    # 在这里执行init
    onMounted( () => {
      init();
    });

    return {
      # 数据
      msg,

      # 方法
      changeMsg
    }
  }
})
</script>
```

# 5.数据监听
```bash
import { defineComponent, ref, watch } from 'vue'

export default defineComponent({
  setup () {
    const name = ref<string>('Petter');

    # 2s后改变数据
    setTimeout(() => {
      name.value = 'Tom';
    }, 2000);

    # 你可以监听一个响应式对象
    watch( name, () => {
      console.log('监听整个 ref ', name.value);
    })

    # 也可以监听对象里面的某个值（此时需要写成 getter 函数）
    watch( () => name.value, () => {
      console.log('只监听 value ', name.value);
    })
  }
})
```
# 6.CSS样式与预处理器
### 使用v-bind动态修改style
```bash
<template>
  <p class="msg">Hello World</p>
</template>

<script lang="ts">
import {defineComponent, ref} from 'vue'

export default defineComponent({
  setup() {
    const fontColor = ref<string>('#ff0000')
    return {
      fontColor
    }
  }
})
</script>

<style scoped>
  .msg {
    color: v-bind(fontColor);
  }
</style>
```

### 样式表的组件作用域
#### style module

**默认**
```bash
<template>
  <p :class="$style.msg">Hello World</p>
</template>

<style module>
.msg {
  color: #ff0000
}
</style>
```
**重命名**
```bash
<template>
  <p :class="classes.msg">Hello World!</p>
</template>

<style module="classes">
.msg {
  color: #ff0000;
}
</style>
```

#### useCssModule
```bash
<template>
  <p :class="$style.msg">
    <span :class="$style.text">Hello World</span>
  </p>
</template>

<script lang="ts">
import {defineComponent, useCssModule } from 'vue'

export default defineComponent({
  setup() {
    const style = useCssModule();
    console.log(style) # {msg: 'App_msg_1tGa9', text: 'App_text_2AEnE'}
  }
})

<style module>
.msg {
  color: #ff0000;
}
.text {
  font-size: 14px;
}
</style>
```