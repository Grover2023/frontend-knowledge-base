# 模版语法

## 文本插值

```vue
<template>
  <div>
    <span>Name:{{ name }}</span>
  </div>
</template>

<script setup>
const name = 'Grover'
</script>
```

双大括号标签会被替换为相应组件实例中 `name` 属性的值。

## 原始 HTML

双大括号会将数据解释为纯文本。若想插入 HTML，你需要使用 `v-html` 指令：

```vue
<template>
  <div v-html="nameCode"></div>
</template>

<script setup>
const nameCode = "<span>Name:Grover</span>"
</script>
```

`v-html` 的作用：在当前组件实例上，将此元素的 innerHTML 与 nameCode 属性保持同步。

## Attribute 绑定

想要响应式的绑定一个 Attribute，应该使用 `v-bind` 指令：

```vue
<template>
  <div>
    <!-- 以下写法等价。 -->
    <span v-bind:name="name">Name</span>
    <span :age="age">Age</span>
    <span :gender>Gender</span>
  </div>
</template>

<script setup>
const name = "Grover"
const age = 18
const gender = 'male'
</script>
```

`v-bind` 指令，指示 Vue 将元素的 Attribute 与组件的属性保持一致。如果绑定的值是 `null` 或 `undefined`，那么该 Attribute 将会从渲染的元素上移除。

## 布尔型 Attribute

### 应用于原生标签

```vue
<template>
  <div>
    <button :disabled="true">Button:true</button>
    <!-- <button disabled>Button:true</button> -->
    <button :disabled="''">Button:''</button>
    <!-- <button disabled>Button:''</button> -->
    <button disabled>Button</button>
    <!-- <button disabled>Button</button> -->
    <button :disabled="false">Button:false</button>
    <!-- <button>Button:false</button> -->
  </div>
</template>
```

当一个布尔型 Attribute 被赋值为一个真值或一个空字符串时，元素会包含该 Attribute 属性。
当一个布尔型 Attribute 被赋值为一个假值时，元素会忽略该 Attribute 属性。

### 应用于自定义组件

```vue
<template>
  <div>
    <MyComponent :is-disabled="true" />
    <!-- Proxy {isDisabled: true} -->
    <MyComponent :is-disabled="''" />
    <!-- Proxy {isDisabled: true} -->
    <MyComponent is-disabled />
    <!-- Proxy {isDisabled: true} -->
    <MyComponent :is-disabled="false" />
    <!-- Proxy {isDisabled: false} -->
  </div>
</template>
```

## 动态绑定多个值

```vue
<template>
  <div v-bind="{ name: 'Grover', age: 18, gender: 'male' }"></div>
  <!-- <div name="Grover" age="18" gender="male"></div> -->
</template>
```

## 使用 JavaScript 表达式

在所有的数据绑定中，都支持完整的 JavaScript 表达式。

```vue
<template>
  {{ number + 1 }}
  {{ ok ? 'YES' : 'NO' }}
  {{ message.split('').reverse().join('') }}
  {{ formatDate(date) }}
  <div :id="`list-${id}`"></div>
</template>
```

## 受限的全局访问

模版中的表达式，将被沙盒化。仅能访问到有限的全局对象列表。