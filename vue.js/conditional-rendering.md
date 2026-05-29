# conditional-rendering

## v-if 和 v-show 的区别

- 可以在 `<template>` 元素上使用 `v-if`，最后的渲染结果不会包含 `<template>` 元素。不可以在 `<template>` 元素上使用 `v-show`。
- `v-if` 是“真实的”按条件渲染，它确保在切换时，条件区块内的事件监听器和子组件，都会被销毁与重建。
- `v-if` 是惰性的：如果在初次渲染时条件为 false，则不会做任何事。当条件首次变为 true 时，才被渲染。有更高的切换开销。
- `v-show` 无论初始条件如何，始终会被渲染，只有 CSS 的 `display` 属性会被切换。有更高的初始渲染开销。