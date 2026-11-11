# transition

`<Transition>` 仅支持单个元素或组件作为其插槽内容。如果内容是一个组件，这个组件必须仅有一个根元素。

## 触发条件

- 由 `v-if` 所触发的切换。
- 由 `v-show` 所触发的切换。
- 由 `<component>` 所触发的切换。
- 由 `key` 属性变化，所触发的切换。

## 案例一

```html
<button @click="show = !show">Toggle</button>
<Transition>
  <p v-if="show">hello</p>
</Transition>
```

```css
/* 下面我们会解释这些 class 是做什么的 */
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
```