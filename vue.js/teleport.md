# teleport

将组件内部的部分模板，传送到该组件 DOM 结构的外层位置去。

## 解决的问题

1. `position: fixed` 相对于浏览器视口定位，有一个条件，不能有任何祖先元素设置 `transform`、`perspective` 或者 `filter` 样式属性。
2. `z-index` 受限于容器元素。

## 案例一

```html
<button @click="open = true">Open Modal</button>

<Teleport to="body">
  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</Teleport>
```

注意：`to` 目标，必须已经存在。