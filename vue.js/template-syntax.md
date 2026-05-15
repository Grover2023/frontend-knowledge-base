# template-syntax

# 文本插值

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