# events

## 基础使用

MyComponent.vue 如下：

```vue
<button @click="$emit('someEvent')">Click Me</button>
```

App.vue 如下：

```vue
<MyComponent @some-event.once="callback" />
```

注意：和原生 DOM 事件不同，组件触发的事件没有冒泡机制。

## 事件参数

MyButton.vue 如下：

```vue
<button @click="$emit('increaseBy', 1)">
  Increase by 1
</button>
```

App.vue 如下：

```vue
<MyButton @increase-by="(n) => count += n" />
```

举例：$emit('foo', 1, 2, 3) 触发后，侦听器函数会收到这 3 个参数值。

## 声明触发的事件

```vue
<script setup>
const emit = defineEmits(['inFocus', 'submit'])

function buttonClick() {
  emit('submit')
}
</script>
```

注意：如果，自定义事件名与原生事件名相同，侦听器函数只会侦听自定义事件，不会侦听原生事件。

## 事件校验

```vue
<script setup>
const emit = defineEmits({
  // 没有校验
  click: null,

  // 校验 submit 事件
  submit: ({ email, password }) => {
    if (email && password) {
      return true
    } else {
      console.warn('Invalid submit event payload!')
      return false
    }
  }
})

function submitForm(email, password) {
  emit('submit', { email, password })
}
</script>
```