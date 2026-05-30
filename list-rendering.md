# list-rendering

## 使用场景

- 遍历数组。
- 遍历对象。遍历的顺序由 `Object.values()` 的返回值来决定。
- 遍历数字。

## 注意事项

- `key` 的作用。帮助 Vue 高效更新虚拟 DOM，避免复用错误。
- `v-for` 可以使用在 `<template>` 元素上。
- `v-if` 高于 `v-for`。所以 `v-if` 无法访问 `v-for` 中的循环变量。
- 子组件标签上使用 `v-for`，必须显示传递数据到子组件。子组件不能直接访问父组件的循环变量。
- `v-for` 可以使用 in、of 进行迭代。迭代时可以进行解构。

## 数组变化侦听

### 变更方法

变更方法，会对调用它们的原数组进行变更。Vue 可以侦听响应式数组的变更方法，在它们被调用时，会触发相关的更新。这些方法包括：

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

### 替换一个数组

非变更方法，如 `filter()`、`concat()`、`slice()`，这些不会更改原数组，而总是返回一个新数组。

当遇到非变更方法时，需要用新数组，替换旧数组。如下：

```js
// `items` 是一个数组的 ref
items.value = items.value.filter((item) => item.message.match(/Foo/))
```