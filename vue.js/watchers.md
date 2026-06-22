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
  console.log('单个计算属性（原始值）', newVal, oldVal, newVal === oldVal); // 单个计算属性（原始值） Grover liu   false
})

watch(firstNameObj, (newVal, oldVal) => {
  console.log('单个计算属性（引用值）', newVal.firstName.value, oldVal.firstName.value, newVal === oldVal); // 单个计算属性（引用值） Grover Grover true
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