# fallthrough-attributes

属性透传，一些没有被子组件声明为 props、自定义事件的属性，依然能传递给子组件。例如：class、style、id 等。

## 快速上手

MyButton.vue 如下：

```vue
<!-- <MyButton> 的模板 -->
<button>Click Me</button>
```

App.vue 如下：

```vue
<MyButton class="large" />
```

最后渲染出的 DOM 结果是：

```vue
<button class="large">Click Me</button>
```

## 对 class 和 style 的合并

MyButton.vue 如下：

```vue
<!-- <MyButton> 的模板 -->
<button class="btn">Click Me</button>
```

App.vue 如下：

```vue
<MyButton class="large" />
```

最后渲染出的 DOM 结果是：

```vue
<button class="btn large">Click Me</button>
```

## 自定义事件继承

规则同样适用。

## 深层组件继承

有些情况下，一个组件会在根节点上，渲染另一个组件。

MyButton.vue 如下：

```vue
<BaseButton />
```

此时，`<MyButton/>` 接收到的透传属性，会继续传给 `<BaseButton/>`。

注意：

1. `<BaseButton/>` 接收到的透传属性，不包含声明过的 props、自定义事件。
2. 透传的属性若符合声明，也可以作为 props 传入 `<BaseButton/>`。

## 禁用属性透传

主要场景：透传的属性，要应用在根节点以外的元素上。

如下：

```vue
<template>
    <div class="btn-wrapper">
        <button class="btn" v-bind="$attrs">Click Me</button>
    </div>
</template>

<script setup>
defineOptions({
  inheritAttrs: false
})
// ...setup 逻辑
</script>
```

## 多根节点的透传

多根节点组件，没有自动属性透传行为。如果 `$attrs` 没有被显示绑定，将会抛出一个运行时警告。

## js 中访问透传属性

```vue
<script setup>
import { useAttrs } from 'vue'

const attrs = useAttrs()
</script>
```

注意，透传属性不是响应式的。如果需要响应性，可以使用 prop、onUpdated 配合。