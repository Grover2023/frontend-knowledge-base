# reactivity-fundamentals

## ref

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