# event-handling

## 基本使用

```vue
<template>
  <div>
    <p>{{ count }}</p>
    <button @click="count++">+1</button>
    <button @click="addWithEvent">+1</button>
    <button @click="addWithEventAndStr($event, 'Hello world.')">+1</button>
    <button @click="(e) => addWithEventAndStr(e, 'Hello world.')">+1</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)

function addWithEvent(e) {
  console.log('e:', e) // e: PointerEvent
  count.value++
}

function addWithEventAndStr(e, str) {
  console.log('e:', e) // e: PointerEvent
  console.log('str:', str) // str: Hello world.
  count.value++
}
</script>
```

## 事件修饰符

```vue
<!-- 单击事件将停止传递 -->
<a @click.stop="doThis"></a>

<!-- 提交事件将不再重新加载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰语可以使用链式书写 -->
<a @click.stop.prevent="doThat"></a>

<!-- 也可以只有修饰符 -->
<form @submit.prevent></form>

<!-- 仅当 event.target 是元素本身时才会触发事件处理器 -->
<!-- 例如：事件处理器不来自子元素 -->
<div @click.self="doThat">...</div>
```

使用修饰符时需要注意调用顺序，因为相关代码是以相同的顺序生成的。因此使用 `@click.prevent.self` 会阻止元素及其子元素的所有点击事件的默认行为，而 `@click.self.prevent` 则只会阻止对元素本身的点击事件的默认行为。

```vue
<!-- 添加事件监听器时，使用 `capture` 捕获模式 -->
<!-- 例如：指向内部元素的事件，在被内部元素处理前，先被外部处理 -->
<div @click.capture="doThis">...</div>

<!-- 点击事件最多被触发一次 -->
<a @click.once="doThis"></a>

<!-- 滚动事件的默认行为 (scrolling) 将立即发生而非等待 `onScroll` 完成 -->
<!-- 以防其中包含 `event.preventDefault()` -->
<div @scroll.passive="onScroll">...</div>
```

请勿同时使用 `.passive` 和 `.prevent`，因为 `.passive` 已经向浏览器表明了，你不想阻止事件的默认行为。如果你这样做了，则 `.prevent` 会被忽略，并且浏览器抛出警告。

## 按键修饰符

## 鼠标按键修饰符