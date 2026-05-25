# reactivity-utilities

## toRef

规范化签名（3.3+）：

```js
// 按原样返回现有的 ref
toRef(existingRef)

// 创建一个只读的 ref，当访问 .value 时会调用此 getter 函数
toRef(() => props.foo)

// 从非函数的值中创建普通的 ref
// 等同于 ref(1)
toRef(1)
```

对象属性签名：

```js
const state = reactive({
  foo: 1,
  bar: 2
})

// 双向 ref，会与源属性同步
const fooRef = toRef(state, 'foo')

// 更改该 ref 会更新源属性
fooRef.value++
console.log(state.foo) // 2

// 更改源属性也会更新该 ref
state.foo++
console.log(fooRef.value) // 3
```

处理 `props`：

```vue
<script setup>
import { toRef } from 'vue'

const props = defineProps(/* ... */)

// 将 `props.foo` 转换为 ref，然后传入
// 一个组合式函数
useSomeFeature(toRef(props, 'foo'))

// getter 语法——推荐在 3.3+ 版本使用
useSomeFeature(toRef(() => props.foo))
</script>
```

当 `toRef` 与 `props` 结合使用时，关于禁止对 `props` 做出更改的限制依然有效。尝试将新的值传递给 ref 对象，等效于尝试直接更改 props 对象，这是不被允许的。

当使用对象属性签名时，即使源属性当前不存在。`toRef` 也会返回一个可用的 ref 对象。这让它在处理可选 props 的时候，非常有用。

## toRefs

```js
const state = reactive({
  foo: 1,
  bar: 2
})

const stateAsRefs = toRefs(state)
/*
stateAsRefs 的类型：{
  foo: Ref<number>,
  bar: Ref<number>
}
*/

// 这个 ref 和源属性已经“链接上了”
state.foo++
console.log(stateAsRefs.foo.value) // 2

stateAsRefs.foo.value++
console.log(state.foo) // 3
```

从组合式函数中，返回响应式对象时，`toRefs` 相当有用。使用它，消费者组件可以解构或展开返回的对象，而不会失去响应式。

```js
function useFeatureX() {
  const state = reactive({
    foo: 1,
    bar: 2
  })

  // ...基于状态的操作逻辑

  // 在返回时都转为 ref
  return toRefs(state)
}

// 可以解构而不会失去响应性
const { foo, bar } = useFeatureX()
```

`toRefs` 在调用时，只会为源对象上可以枚举的属性，创建 ref 对象。

## unref

如果参数是 ref 对象，则返回内部值。否则，返回参数本身。这是 `val = isRef(val) ? val.value : val` 的语法糖。

```js
function useFoo(x: number | Ref<number>) {
  const unwrapped = unref(x)
  // unwrapped 现在保证为 number 类型
}
```

## readonly

接收一个普通对象、响应式对象、ref 对象，返回一个原值的只读代理。深层次的。

```js
const original = reactive({ count: 0 })

const copy = readonly(original)

watchEffect(() => {
  // 用来做响应性追踪
  console.log(copy.count)
})

// 更改源属性会触发其依赖的侦听器
original.count++

// 更改该只读副本将会失败，并会得到一个警告
copy.count++ // warning!
```

## isRef、isReactive

```vue
<script setup>
import { ref, shallowRef, reactive, shallowReactive, isRef, isReactive } from 'vue'
const obj = {
  a: 1,
  b: 2,
  c: {
    d: 3,
    e: 4
  }
}

const state1 = ref(obj)
const state2 = shallowRef(obj)
const state3 = reactive(obj)
const state4 = shallowReactive(obj)

console.log(isRef(state1)) // true
console.log(isRef(state2)) // true
console.log(isRef(state1.value.c)) // false
console.log(isRef(state2.value.c)) // false
console.log(isReactive(state1.value.c)) // true
console.log(isReactive(state2.value.c)) // false
console.log(isReactive(state3)) // true
console.log(isReactive(state4)) // true
console.log(isReactive(state3.c)) // true
console.log(isReactive(state4.c)) // false
</script>
```

## isProxy

```vue
<script setup>
import { reactive, readonly, shallowReactive, shallowReadonly, isProxy } from 'vue'

const reactiveObject = reactive({ message: 'Hello' })
const readonlyObject = readonly({ message: 'Hello' })
const shallowReactiveObject = shallowReactive({ message: 'Hello' })
const shallowReadonlyObject = shallowReadonly({ message: 'Hello' })
const normalObject = { message: 'Hello' }

console.log(isProxy(reactiveObject)) // true
console.log(isProxy(readonlyObject)) // true
console.log(isProxy(shallowReactiveObject)) // true
console.log(isProxy(shallowReadonlyObject)) // true
console.log(isProxy(normalObject)) // false
</script>
```

## toValue

```js
toValue(1) //       --> 1
toValue(ref(1)) //  --> 1
toValue(() => 1) // --> 1
```

```js
import type { MaybeRefOrGetter } from 'vue'

function useFeature(id: MaybeRefOrGetter<number>) {
  watch(() => toValue(id), id => {
    // 处理 id 变更
  })
}

// 这个组合式函数支持以下的任意形式：
useFeature(1)
useFeature(ref(1))
useFeature(() => 1)
```