---
title: 3_vue3学习之组件之间的通信
categories: Vue
tags: vue3
date: 2022-01-12 16:20:00
---
# 1.使用props
```bash
export default defineComponent({
  props: {
    title: String,
    index: number,
  }
  setup (props) {
    # 该入参包含了我们定义的所有props
    console.log(props)
  }
})
```

# 2.获取非prop的Attribute
```bash
export default defineComponent({
  setup (props, { attrs }) {
    # attrs 是个对象，每个 Attribute 都是它的 key
    console.log(attrs.class);

    # 如果传下来的 Attribute 带有短横线，需要通过这种方式获取
    console.log(attrs['data-hash']);
  }
})

<template>
  <!-- 指定继承 -->
  <p v-bind="attrs"></p>
  <!-- 指定继承 -->
  
  <!-- 这些不会自动继承 -->
  <p></p>
  <p></p>
  <p></p>
  <!-- 这些不会自动继承 -->
</template>
```

# 3.emits的使用
## emits时做一些校验
```bash
# chid中
export default defineComponent({
  name: 'HelloWorld',
  emits: {
    # 需要校验
    'update-age': (age: number) => {
      # 写一些条件拦截，记得返回false
      if (age < 18) {
        console.log('未成年不允许参与')
        return false
      }
      # 通过则返回true'
      return true
    }
  },
  props: {
  },
  setup (prop, {emit }) {
    const updateAge = function () {
      emit('update-age', 20)
    }
    return {
      updateAge,
    }
  }
});
</script>
```

## emits父级绑定
```bash
<script lang="ts">
import { defineComponent} from 'vue'
import Child from '@cp/Child.vue'

export default defineComponent({
  components: {
    Child
  },

  setup() {
    # emit调用
    const updateAge = function (data) {
      console.log(data, 897987)
    }
    return {
      updateAge
    }
  },
})
</script>
```