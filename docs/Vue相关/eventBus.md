## eventBus 使用方法

> 1.创建一个 vue 实例，作为事件总线

```js
import Vue from "vue";
const eventBus = new Vue();
export default eventBus;
```

> 2.在需要监听的事件中，使用 eventBus.$on()方法监听事件

```js
//child1.vue
<template>
    <div></div>
  </template>

  <script>
  import eventBus from './eventBus'
  export default {
    name: "alert",
    created() {
      // 在 created 的時候在 Vue 底下註冊監聽 alert:message 這個事件
      eventBus.$on("alert:message", msg => {
        // 並將接收到的 message 傳給自己的 methods showAlert 去觸發 alert 事件。
        this.showAlert(msg);
      });
    },
    methods: {
      showAlert(msg) {
        alert(msg);
      },
    },
    beforeDestroy: function() {
      // 元件銷毀前要註銷監聽事件
      eventBus.$off("alert:message");
    }
  };
  </script>
```

> 3 创建 emit 点击事件

```js
//child2.vue
<template>
    <div>
      <!-- button 點擊觸發 doShowAlert 並帶入要傳入的訊息 -->
      <button @click="doShowAlert(message)">doShowAlerts</button>
    </div>
  </template>

  <script>
  import eventBus from './eventBus'

  export default {
    name: "HelloWorld",
    data() {
      return {
        // msg 也可以傳入一個物件，自定義需要的內容，不一定只能傳入字串
        message: "我是寫在helloworld.vue的,訊息",
      };
    },
    methods: {
      // 把事件 emit 出去
      doShowAlert(msg) {
        eventBus.$emit("alert:message", msg);
      },
    }
  };
  </script>
```
