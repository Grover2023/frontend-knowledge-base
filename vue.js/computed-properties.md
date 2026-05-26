# computed-properties

## 基础示例

只读的计算属性:

```vue
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})

// 返回值是一个计算属性 ref 对象。
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Yes' : 'No'
})
</script>

<template>
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

可写的计算属性：

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // 注意：我们这里使用的是解构赋值语法
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

获取上一个值的计算属性：

```vue
<script setup>
import { ref, computed } from 'vue'

const count = ref(2)

// 这个计算属性在 count 的值小于或等于 3 时，将返回 count 的值。
// 当 count 的值大于等于 4 时，将会返回满足我们条件的最后一个值
// 直到 count 的值再次小于或等于 3 为止。
const alwaysSmall = computed((previous) => {
  if (count.value <= 3) {
    return count.value
  }

  return previous
})
</script>
```

## 特点

一个计算属性的声明中描述的是如何根据其他值派生一个值。

计算属性会基于其响应式依赖被缓存。响应式依赖更新时，计算属性才会重新计算。

下面的计算属性，永远也不会更新。因为 `Date.now()` 并不是一个响应式依赖：

```js
const now = computed(() => Date.now())
```

## 最佳实践

### Getter 不应该有副作用

常见的副作用操作有：

- 调用系统 I/O 的 API。
- 发送网络请求。
- 在函数体内修改外部变量。
- 使用 console.log 输出。
- 调用存在副作用的函数。
- 修改 DOM 对象。

### 避免直接修改计算属性