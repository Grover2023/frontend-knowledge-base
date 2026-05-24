# reactivity-fundamentals

## ref

简单使用：

```vue
<template>
  <div>
    <p>Name:{{ name }}</p>
    <p>Name:{{ tony.name }}; Age:{{ tony.age }}; Habits:{{ tony.habits }}; City:{{ tony.address.city }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const name = ref('Grover');
const tony = ref({
  name: 'Tony',
  age: 18,
  habits: [],
  address: {
    city: 'ShangHai',
  }
});

setTimeout(() => {
  name.value = 'Tom';
  tony.value.name = 'Bill';
  tony.value.age = 28;
  tony.value.habits = ['唱', '跳', 'Rap', '篮球'];
  tony.value.address.city = 'HangZhou';
}, 2000);
</script>
```

在模版中，`ref` 类型的响应式数据，会自动解包，访问时不需要 `.value`。

## shallowRef

简单使用：

```vue
<template>
  <div>
    <p>Name:{{ tony.name }}; Age:{{ tony.age }}; Habits:{{ tony.habits }}; City:{{ tony.address.city }}</p>
  </div>
</template>

<script setup>
import { shallowRef } from 'vue';

const tony = shallowRef({
  name: 'Tony',
  age: 18,
  habits: [],
  address: {
    city: 'ShangHai',
  }
});

setTimeout(() => {
  // 下列操作不会触发更新。
  tony.value.name = 'Bill';
  tony.value.age = 28;
  tony.value.habits = ['唱', '跳', 'Rap', '篮球'];
  tony.value.address.city = 'HangZhou';
}, 2000);

setTimeout(() => {
  // 下列操作会触发更新。
  tony.value = {
    name: 'Tom',
    age: 38,
    habits: ['干饭'],
    address: {
      city: 'BeiJing',
    }
  }
}, 5000)
</script>
```

对于 `shallowRef` 来说，只有对 `.value` 的访问会被追踪。

## DOM 更新时间

当修改响应式数据时，DOM 会被自动更新。但需要注意的是，DOM 更新不是同步的。

要等待 DOM 更新完成后，再执行一些额外代码，可以使用 `nextTick`：

```vue
<template>
  <div class="container">Habits:{{ habits }}</div>
</template>

<script setup>
import { nextTick, onMounted, ref } from "vue";

let container = null;
const habits = ref(['唱', '跳', 'Rap', '篮球']);

setTimeout(async () => {
  habits.value.push('好好学习', '天天向上');
  console.log('container.innerText:', container.innerText); // container.innerText: Habits:[ "唱", "跳", "Rap", "篮球" ]
  await nextTick();
  console.log('container.innerText:', container.innerText); // container.innerText: Habits:[ "唱", "跳", "Rap", "篮球", "好好学习", "天天向上" ]
}, 2000);

onMounted(() => {
  container = document.querySelector('.container');
})
</script>
```

## reactive

当 `ref` 的值是一个对象时，`ref()` 会在内部调用 `reactive`。

值得注意的是，`reactive()` 返回的是一个原始对象的 Proxy。它和原始对象是不相等的：

```js
const raw = {}
const proxy = reactive(raw)

// 代理对象和原始对象不是全等的
console.log(proxy === raw) // false
```

对同一个原始对象调用 `reactive()` 会总是返回同样的代理对象，而对一个已存在的代理对象调用 `reactive()` 会返回其本身：

```js
// 在同一个对象上调用 reactive() 会返回相同的代理
console.log(reactive(raw) === proxy) // true

// 在一个代理上调用 reactive() 会返回它自己
console.log(reactive(proxy) === proxy) // true
```

简单使用：

```vue
<template>
  <div>
    <p>Name:{{ tony.name }}; Age:{{ tony.age }}; Habits:{{ tony.habits }}; City:{{ tony.address.city }}</p>
  </div>
</template>

<script setup>
import { reactive } from 'vue';

const tony = reactive({
  name: 'Tony',
  age: 18,
  habits: [],
  address: {
    city: 'ShangHai',
  }
});

setTimeout(() => {
  tony.name = 'Bill';
  tony.age = 28;
  tony.habits = ['唱', '跳', 'Rap', '篮球'];
  tony.address.city = 'HangZhou';
}, 2000);
</script>
```

### reactive 的局限性

1. 使用 reactive 创建响应式数据的时候，值的类型是有限的：

  - 只能是对象类型（object、array、map、set）。
  - 不能是原始类型（string、number、boolean）。

2. 不能去替换响应式对象，否则会丢失响应式的追踪。

  ```js
  let state = reactive({ count: 0 })

  // 上面的 ({ count: 0 }) 引用将不再被追踪
  // (响应性连接已丢失！)
  state = reactive({ count: 1 })
  ```

3. 解构时，会丢失响应式。

  ```js
  const state = reactive({ count: 0 })

  // 当解构时，count 已经与 state.count 断开连接
  let { count } = state
  // 不会影响原始的 state
  count++

  // 该函数接收到的是一个普通的数字
  // 并且无法追踪 state.count 的变化
  // 我们必须传入整个对象以保持响应性
  callSomeFunction(state.count)
  ```

## 解包细节

1. ref 对象作为 reactive 对象的属性，会自动解包。

  ```vue
  <script setup>
  import { ref, reactive } from 'vue';

  const name = ref('Grover');
  const grover = reactive({ name });

  console.log('grover.name:', grover.name) // grover.name: Grover
  console.log('name.value:', name.value) // name.value: Grover
  </script>
  ```

2. ref 对象作为 shallowReactive 对象的属性，不会自动解包。

  ```vue
  <script setup>
  import { ref, shallowReactive } from 'vue';

  const name = ref('Grover');
  const grover = shallowReactive({ name });

  console.log('grover.name.value:', grover.name.value); // grover.name.value: Grover
  console.log('name.value:', name.value); // name.value: Grover
  </script>
  ```

3. ref 对象作为数组、集合的元素时，不会自动解包。

  ```js
  const books = reactive([ref('Vue 3 Guide')])
  // 这里需要 .value
  console.log(books[0].value)

  const map = reactive(new Map([['count', ref(0)]]))
  // 这里需要 .value
  console.log(map.get('count').value)
  ```

4. 出现在模板中时，顶级的 ref 对象，会自动解包。非顶级的 ref 对象，不会自动解包。

  ```vue
  <template>
    <div>
      <p>Name:{{ name }}; Age:{{ age.data.value }}</p>
    </div>
  </template>

  <script setup>
  import { ref } from 'vue';

  const name = ref('Grover');
  const age = { data: ref(18) };
  </script>
  ```