# components-basics

## 基础使用

GreetingComponent.vue 如下：

```vue
<template>
    <h1>{{ greetingMessage }}</h1>
</template>

<script setup>
defineProps(["greetingMessage"]);
</script>
```

App.vue 如下：

```vue
<template>
  <GreetingComponent :greeting-message="msg" />
</template>

<script setup>
import { ref } from "vue";
import GreetingComponent from "@/components/GreetingComponent.vue";

const msg = ref("Hello world.");
</script>
```

## 响应式 Props 结构

在 3.5 及以上版本，当在同一个 `<script setup>` 代码块中，访问由 `defineProps` 解构的变量时，Vue 编译器会自动在前面添加 `props.`。下方两段代码等价。

```vue
const { foo } = defineProps(['foo'])

watchEffect(() => {
  // 在 3.5 之前只运行一次
  // 在 3.5+ 中在 "foo" prop 变化时重新执行
  console.log(foo)
})
```

```vue
const props = defineProps(['foo'])

watchEffect(() => {
  // `foo` 由编译器转换为 `props.foo`
  console.log(props.foo)
})
```

## 传递 Props 细节

### Number

```html
<!-- 虽然 `42` 是个常量，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost :likes="42" />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :likes="post.likes" />
```

### Boolean

```html
<!-- 仅写上 prop 但不传值，会隐式转换为 `true` -->
<BlogPost is-published />

<!-- 虽然 `false` 是静态的值，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost :is-published="false" />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :is-published="post.isPublished" />
```

### Array

```html
<!-- 虽然这个数组是个常量，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost :comment-ids="[234, 266, 273]" />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :comment-ids="post.commentIds" />
```

### Object

```html
<!-- 虽然这个对象字面量是个常量，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
 />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :author="post.author" />
```

### 使用一个对象绑定多个 Props

```vue
<template>
  <BlogPost v-bind="post" />
</template>

<script setup>
import BlogPost from "@/components/BlogPost.vue";

const post = {
  id: 1,
  title: 'My Journey with Vue'
}
</script>
```

## 单项数据流

不应该在子组件，修改 Props。

常见场景：

1. Props 作为子组件局部状态的初始值：

```vue
const props = defineProps(['initialCounter'])

// 计数器只是将 props.initialCounter 作为初始值
// 像下面这样做就使 prop 和后续更新无关了
const counter = ref(props.initialCounter)
```

2. 基于 Props 做进一步的转化：

```vue
const props = defineProps(['size'])

// 该 prop 变更时计算属性也会自动更新
const normalizedSize = computed(() => props.size.trim().toLowerCase())
```

## Props 校验

```vue
defineProps({
  // 基础类型检查
  // (给出 `null` 和 `undefined` 值则会跳过任何类型检查)
  propA: Number,
  // 多种可能的类型
  propB: [String, Number],
  // 必传，且为 String 类型
  propC: {
    type: String,
    required: true
  },
  // 必传但可为 null 的字符串
  propD: {
    type: [String, null],
    required: true
  },
  // Number 类型的默认值
  propE: {
    type: Number,
    default: 100
  },
  // 对象类型的默认值
  propF: {
    type: Object,
    // 对象或数组的默认值
    // 必须从一个工厂函数返回。
    // 该函数接收组件所接收到的原始 prop 作为参数。
    default(rawProps) {
      return { message: 'hello' }
    }
  },
  // 自定义类型校验函数
  // 在 3.4+ 中完整的 props 作为第二个参数传入
  propG: {
    validator(value, props) {
      // The value must match one of these strings
      return ['success', 'warning', 'danger'].includes(value)
    }
  },
  // 函数类型的默认值
  propH: {
    type: Function,
    // 不像对象或数组的默认，这不是一个
    // 工厂函数。这会是一个用来作为默认值的函数
    default() {
      return 'Default function'
    }
  }
})
```

注意：`defineProps()` 中的参数，不可以访问 `<script setup>` 中定义的其他变量。因为，在编译时，整个表达式都会被移到外部函数中。

一些补充细节：

1. 所有 Props 默认都是可选的。除非声明了 `required: true`。
2. 除了 `Boolean` 类型以外，未传递的可选 Props，默认值为 `undefined`。
3. `Boolean` 类型的，未传递的 Props，将被转换为 `false`。

### 运行时类型检查

校验选项中的 `type` 可以是下列这些原生的构造函数：

- String
- Number
- Boolean
- Array
- Object
- Date
- Function
- Symbol
- Error

`type` 也可以是，自定义的类或者构造函数：

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName
    this.lastName = lastName
  }
}
```

```js
defineProps({
  author: Person
})
```

### 可为 null 的类型

```js
defineProps({
  id: {
    type: [String, null],
    required: true
  }
})
```

## Boolean 类型转化

单类型时：

```js
defineProps({
  disabled: Boolean
})
```

```html
<!-- 等同于传入 :disabled="true" -->
<MyComponent disabled />

<!-- 等同于传入 :disabled="false" -->
<MyComponent />
```

多类型时：

```js
// disabled 将被转换为 true
defineProps({
  disabled: [Boolean, Number]
})

// disabled 将被转换为 true
defineProps({
  disabled: [Boolean, String]
})

// disabled 将被转换为 true
defineProps({
  disabled: [Number, Boolean]
})

// disabled 将被解析为空字符串 (disabled="")
defineProps({
  disabled: [String, Boolean]
})
```