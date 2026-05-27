# class-and-style-bindings

## 绑定 HTML class

```vue
<template>
  <div>
    <p class="static" :class="{ active: isActive, 'text-danger': hasError }">Apple</p>
    <!-- <p class="static active">Apple</p> -->
    <p :class="classObject">Orange</p>
    <!-- <p class="active">Orange</p> -->
    <p :class="classObjectComputed">Bananas</p>
    <!-- <p class="active">Bananas</p> -->
    <p :class="[activeClass, errorClass]">Strawberry</p>
    <!-- <p class="active text-danger">Strawberry</p> -->
    <p :class="[isActive ? activeClass : '', errorClass]">Blueberry</p>
    <!-- <p class="active text-danger">Blueberry</p> -->
    <p :class="[{ [activeClass]: isActive }, errorClass]">Grape</p>
    <!-- <p class="active text-danger">Grape</p> -->
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue';

const isActive = ref(true);
const hasError = ref(false);
const activeClass = ref('active')
const errorClass = ref('text-danger')

const classObject = reactive({
  active: true,
  'text-danger': false
})

const classObjectComputed = computed(() => ({
  active: isActive.value,
  'text-danger': hasError.value
}));
</script>
```

## 绑定内联样式

```vue
<template>
  <div>
    <p :style="{ color: activeColor, fontSize: fontSize + 'px' }">Apple</p>
    <!-- <p style="color: red; font-size: 30px;">Apple</p> -->
    <p :style="{ color: activeColor, 'font-size': fontSize + 'px' }">Orange</p>
    <!-- <p style="color: red; font-size: 30px;">Orange</p> -->
    <p :style="baseStyles">Bananas</p>
    <!-- <p style="color: red; font-size: 30px;">Bananas</p> -->
    <p style="color: red" :style="'font-size: 1em'">Strawberry</p>
    <!-- <p style="color: red; font-size: 1em;">Strawberry</p> -->
    <p :style="[baseStyles, overridingStyles]">Blueberry</p>
    <!-- <p style="color: red; font-size: 60px;">Blueberry</p> -->
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue';

const activeColor = ref('red');
const fontSize = ref(30);

const baseStyles = reactive({
  color: 'red',
  fontSize: '30px'
})
const overridingStyles = reactive({
  fontSize: '60px'
})
</script>
```