# form-input-bindings

## 基本使用

`v-model` 会忽略，任何表单元素上，初始的 `value`、`checked`、`selected` 等 attribute。将当前绑定的 JavaScript 状态视为正确的数据来源。

```vue
<template>
  <div>
    <div>
      <!-- 对于中文、日文等，会在拼字阶段触发更新。 -->
      <p>Text:{{ text }}</p>
      <input type="text" :value="text" @input="event => text = event.target.value" />
    </div>
    <div>
      <!-- 对于中文、日文等，v-model 不会在拼字阶段触发更新。 -->
      <p>Message:{{ message }}</p>
      <input type="text" v-model="message" />
    </div>
    <div>
      <p style="white-space: pre-line;">MultilineMessage:{{ multilineMessage }}</p>
      <textarea v-model="multilineMessage" />
    </div>
    <div>
      <input type="checkbox" id="checkbox-status" v-model="checkboxStatus" />
      <label for="checkbox-status">{{ checkboxStatus }}</label>
    </div>
    <div>
      <input type="checkbox" id="checkbox-status-custom" v-model="checkboxStatusCustom" :true-value="trueValue"
        :false-value="falseValue" />
      <label for="checkbox-status-custom">{{ checkboxStatusCustom }}</label>
    </div>
    <div>
      <p>CheckboxStatusList:{{ checkboxStatusList }}</p>
      <input type="checkbox" id="jack" value="jack" v-model="checkboxStatusList" />
      <label for="jack">jack</label>
      <input type="checkbox" id="john" value="john" v-model="checkboxStatusList" />
      <label for="john">john</label>
      <input type="checkbox" id="mike" value="mike" v-model="checkboxStatusList" />
      <label for="mike">mike</label>
    </div>
    <div>
      <p>Picked:{{ picked }}</p>
      <input type="radio" id="one" value="one" v-model="picked" />
      <label for="one">one</label>
      <input type="radio" id="two" value="two" v-model="picked" />
      <label for="two">two</label>
    </div>
    <div>
      <!-- 兼容 IOS，常用做法。 -->
      <p>Selected:{{ selected }}</p>
      <select v-model="selected">
        <option value="" disabled>Please select one</option>
        <option v-for="item in options" :value="item.value" :key="item.value">{{ item.text }}</option>
      </select>
    </div>
    <div>
      <!-- 兼容 IOS，常用做法。 -->
      <p>selectedList:{{ selectedList }}</p>
      <select v-model="selectedList" multiple>
        <option v-for="item in options" :value="item.value" :key="item.value">{{ item.text }}</option>
      </select>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const text = ref('');
const message = ref('');
const multilineMessage = ref('');
const checkboxStatus = ref(false);
const checkboxStatusCustom = ref('no');
const trueValue = ref('yes');
const falseValue = ref('no');
const checkboxStatusList = ref([]);
const picked = ref('');
const selected = ref('');
const selectedList = ref([]);

const options = [
  { text: 'a', value: 'a' },
  { text: 'b', value: 'b' },
  { text: 'c', value: 'c' },
  { text: 'd', value: 'd' },
  { text: 'f', value: 'f' },
]
</script>
```

## 修饰符

### .lazy

调整为 `change` 事件后，更新数据。

### .number

将输入内容，自动转成数字。如果该值无法被 `parseFloat` 处理，那么将返回最开始的输入内容。

`.number` 修饰符，会在输入框有 `type="number"` 时自动启用。

### .trim

自动去除用户输入内容的两段空格。