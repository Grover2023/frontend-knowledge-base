# plugins

## 使用场景

1. 通过 `app.component()` 和 `app.directive()` 注册全局组件或自定义指令。
2. 通过 `app.provide()` 将数据注入整个应用。
3. 向 `app.config.globalProperties` 添加属性或方法。

## 案例：i18n

```js
// plugins/i18n.js
export default {
  install: (app, options) => {
    // 注入一个全局可用的 $translate() 方法
    app.config.globalProperties.$translate = (key) => {
      // 获取 `options` 对象的深层属性
      // 使用 `key` 作为索引
      return key.split('.').reduce((o, i) => {
        if (o) return o[i]
      }, options)
    }
  }
}
```

```js
// main.js
import i18nPlugin from './plugins/i18n'

app.use(i18nPlugin, {
  greetings: {
    hello: 'Bonjour!'
  }
})
```

```html
<!-- 业务组件 -->
<h1>{{ $translate('greetings.hello') }}</h1>
```

## 案例：全局错误处理和日志插件

```js
// plugins/ErrorLogger/error-logger.js
import ErrorLogger from './ErrorLogger.vue'

export default {
  install(app, options = {}) {
    // 1. 首先进行参数归一化

    // 设置一个默认的 options
    const defaultOptions = {
      logToConsole: true, // 是否把错误日志打印到控制台
      remotoLogging: false, // 是否把错误日志发送到服务器
      remoteUrl: '' // 远程日志服务器地址
    }

    // 合并用户传入的 options 和默认 options
    const config = { ...defaultOptions, ...options }

    // 2. 捕获两种类型的错误

    // （1）全局Vue错误
    app.config.errorHandler = (err, vm, info) => {
      logError(err, info)
    }

    // （2）捕获未处理的 Promise 错误
    window.addEventListener('unhandledrejection', (event) => {
      logError(event.reason, 'unhandled promise rejection error!!!')
    })

    // 3. 统一交给错误处理函数处理

    // 错误处理函数
    function logError(error, info) {
      // 是否在控制台输出
      if (config.logToConsole) {
        // 并且console.error方法是改写过的，会把error信息记录到errors数组里面
        console.error(`[错误：${info}]`, error)
      }

      // 是否发送到远程服务器
      if (config.remotoLogging && config.remoteUrl) {
        fetch(config.remoteUrl, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            error: error.message, // 错误消息
            stack: error.stack, // 错误堆栈
            info, // 具体错误说明信息
            time: new Date().toISOString() // 记录时间
          })
        }).catch(console.error)
      }
    }

    // 4. 注册 ErrorLogger 组件
    app.component('ErrorLogger', ErrorLogger)
  }
}
```

```vue
<!-- plugins/ErrorLogger/ErrorLogger.vue -->
<template>
  <div v-if="errors.length">
    <h1>错误日志</h1>
    <ul>
      <li v-for="error in errors" :key="error.time">{{ error.time }} - {{ error.message }}</li>
    </ul>
  </div>
</template>

<script setup>
import { reactive, onMounted } from 'vue'

// 该数组用于存储错误信息
const errors = reactive([])

onMounted(() => {
  // 改写 console.error 方法
  // 之后在使用 console.error 方法打印错误的时候
  // 会自动将错误信息推入到 errors 数组中
  const oldConsoleError = console.error
  console.error = (...args) => {
    // 将错误信息推入到 errors 数组中
    errors.push({
      message: args[0],
      time: new Date().toDateString()
    })
    oldConsoleError.apply(console, args)
  }
})
</script>
```

```js
// main.js
app.use(ErrorLogger, {
  logToConsole: true,
  remotoLogging: true,
  remoteUrl: 'http://localhost:3000/log'
})
```