# component-v-model

## 基础使用

Child.vue 如下：

```vue
<template>
    <div>Parent bound v-model is: {{ model }}</div>
    <div>Parent bound v-model is: <input v-model="model" /></div>
    <button @click="update">Increment</button>
</template>

<script setup>
const model = defineModel()

function update() {
    model.value++
}
</script>
```

Parent.vue 如下：

```vue
<template>
    <div>
        <Child v-model="countModel" />
    </div>
</template>

<script setup>
import { ref } from "vue";
import Child from "./Child.vue";

const countModel = ref(0);
</script>
```

## 声明底层 Props 的选项

```js
// 使 v-model 必填
const model = defineModel({ required: true })

// 提供一个默认值
const model = defineModel({ default: 0 })

// 指定类型
const model = defineModel({ type: String })
```

## v-model 参数

UserName.vue 如下：

```vue
<script setup>
const firstName = defineModel('firstName')
const lastName = defineModel('lastName')
</script>

<template>
  <input type="text" v-model="firstName" />
  <input type="text" v-model="lastName" />
</template>
```

App.vue 如下：

```vue
<UserName
  v-model:first-name="first"
  v-model:last-name="last"
/>
```

## v-model 修饰符

MyComponent.vue 如下：

```vue
<script setup>
const [model, modifiers] = defineModel({
  required: true,
  set(value) {
    if (modifiers.capitalize) {
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
    return value
  }
})
</script>

<template>
  <input type="text" v-model="model" />
</template>
```

App.vue 如下：

```vue
<MyComponent v-model.capitalize="myText" />
```

## 补充

1. 携带参数的 `v-model`，如果需要额外的 Props 选项，应该在 model 名称之后传递：

```vue
const title = defineModel('title', { required: true, type: String })
```

2. 携带参数的 `v-model`，使用修饰符的方法如下：

UserName.vue 如下：

```vue
<script setup>
const [firstName, firstNameModifiers] = defineModel('firstName')
const [lastName, lastNameModifiers] = defineModel('lastName')

console.log(firstNameModifiers) // { capitalize: true }
console.log(lastNameModifiers) // { uppercase: true }
</script>
```

App.vue 如下：

```vue
<UserName
  v-model:first-name.capitalize="first"
  v-model:last-name.uppercase="last"
/>
```