# watchers

## 侦听数据源类型

可以是：ref(包括计算属性)、响应式对象、getter、多个数据源组成的数组：

```vue
<template>
  <div></div>
</template>

<script setup>
import { computed, reactive, ref, watch } from 'vue';

const firstName = ref('');
const lastName = ref('');
const fullName = computed(() => {
  return `${firstName.value} ${lastName.value}`;
})
const firstNameObj = computed(() => {
  return { firstName };
})
const countObj = reactive({ count: 0 });

setTimeout(() => {
  firstName.value = 'Grover';
  lastName.value = 'liu';
  countObj.count = 1;
}, 1000);

watch(firstName, (newVal, oldVal) => {
  console.log('单个 ref：', newVal, oldVal, newVal === oldVal); // 单个 ref： Grover  false
})

watch(fullName, (newVal, oldVal) => {
  console.log('单个计算属性（返回原始值）', newVal, oldVal, newVal === oldVal); // 单个计算属性（原始值） Grover liu   false
})

watch(firstNameObj, (newVal, oldVal) => {
  console.log('单个计算属性（返回引用值）', newVal.firstName.value, oldVal.firstName.value, newVal === oldVal); // 单个计算属性（引用值） Grover Grover true
}, { deep: true })

watch(countObj, (newVal, oldVal) => {
  console.log('单个响应式对象', newVal.count, oldVal.count, newVal === oldVal); // 单个响应式对象 1 1 true
})

watch(() => countObj.count, (newVal, oldVal) => {
  console.log('单个 getter（返回原始值）', newVal, oldVal, newVal === oldVal); // 单个 getter 1 0 false
})

watch(() => countObj, (newVal, oldVal) => {
  console.log('单个 getter（返回引用值）', newVal.count, oldVal.count, newVal === oldVal); // 单个 getter（返回引用值） 1 1 true
}, { deep: true })

watch([firstName, () => lastName.value], ([newA, newB], [oldA, oldB]) => {
  console.log('多个监听源', newA, newB, oldA, oldB, newA === oldA, newB === oldB); // 多个监听源 Grover liu   false false
})
</script>
```

## 即时回调的侦听器

watch 默认是懒执行的：仅当数据源变化时，才会执行回调。

通过配置 `immediate: true`，让回调函数立即执行。且当数据源变化时，再次执行。

## 一次性侦听器

通过配置 `once: true`，让回调函数，在数据源变化时，只执行一次。

## watchEffect

会自动跟踪回调函数中的响应式依赖。依赖变化时，回调函数重新执行。

回调函数会立即执行，不需要 `immediate: true`。

只跟踪回调函数中被使用到的属性，而不是递归的跟踪所有的属性。

watchEffect 在回调函数同步执行期间，才会追踪依赖。在使用异步回调时，只有在第一个 `await` 正常工作前，访问到的属性才会被追踪。