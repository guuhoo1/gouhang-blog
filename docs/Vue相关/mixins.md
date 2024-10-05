## 前言

当我们的项目越来越大，我们会发现组件之间可能存在很多相似的功能，你在一遍又一遍的复制粘贴相同的代码段（data，method，watch、mounted 等），如果我们在每个组件中去重复定义这些属性和方法会使得项目出现代码冗余并提高了维护难度，针对这种情况官方提供了 Mixins 特性

# 一、什么是 Mixins？

mixins（混入），[官方](https://v2.cn.vuejs.org/v2/guide/mixins.html)的描述是一种分发 Vue 组件中可复用功能的非常灵活的方式，mixins 是一个 js 对象，它可以包含我们组件中 script 项中的任意功能选项，如 data、components、methods 、created、computed 等等。我们只要将共用的功能以对象的方式传入 mixins 选项中，当组件使用 mixins 对象时所有 mixins 对象的选项都将被混入该组件本身的选项中来，这样就可以提高代码的重用性，使你的代码保持干净和易于维护。

# 二、什么时候使用 Mixins？

当我们存在多个组件中的数据或者功能很相近时，我们就可以利用 mixins 将公共部分提取出来，通过 mixins 封装的函数，组件调用他们是不会改变函数作用域外部的。

# 三、如何创建 Mixins？

在 src 目录下创建一个 mixins 文件夹，文件夹下新建一个 myMixins.js 文件。前面我们说了 mixins 是一个 js 对象，所以应该以对象的形式来定义 myMixins，在对象中我们可以和 vue 组件一样来定义我们的 data、components、methods 、created、computed 等属性，并通过 export 导出该对象

```js
// mixins/myMixins.js
var mixin = {
  data() {
    return {
      eventStatus: {}
    }
  },
  methods: {
    addEventState(eventName) {
      const eventStatusClone = { ...this.eventStatus }
      eventStatusClone[eventName] = true
      this.eventStatus = eventStatusClone
    },
    removeEventState(eventName) {
      const eventStatusClone = { ...this.eventStatus }
      delete eventStatusClone[eventName]
      this.eventStatus = eventStatusClone
    },
    hasEventState(eventName) {
      return Boolean(this.eventStatus[eventName])
    },
    controlEventStatus(eventName, fn) {
      if (typeof eventName !== 'string' || eventName.trim() === '') {
        throw new Error('eventName must be a non-empty string')
        return
      }
      if (typeof fn !== 'function') {
        throw new Error('fn must be a function')
        return
      }
      if (this.hasEventState(eventName)) return
      this.addEventState(eventName)
      return (...args) => {
        return fn(...args)
          .catch(err => {})
          .finally(() => {
            this.removeEventState(eventName)
          })
      }
    }
  }
}
export default mixin
```

> 使用 mixin

```js
import mixin from './mixins'
export default {
  mixins: [mixin]
}
```

# 四、Mixins 的特点

**_方法和参数在各组件中不共享，虽然组件调用了 mixins 并将其属性合并到自身组件中来了，但是其属性只会被当前组件所识别并不会被共享，也就是其他组件无法从当前组件中获取到 mixins 中的数据和方法_**

# 五、Mixins 合并冲突

**值为对象(components、methods 、computed、data)的选项，混入组件时选项会被合并，键冲突时优先组件，组件中的键会覆盖混入对象的**

**值为函数(created、mounted)的选项，混入组件时选项会被合并调用，混合对象里的钩子函数在组件里的钩子函数之前调用**

# 与 vuex 的区别

- vuex：用来做状态管理的，里面定义的变量在每个组件中均可以使用和修改，在任一组件中修改此变量的值之后，其他组件中此变量的值也会随之修改。

* Mixins：可以定义共用的变量，在每个组件中使用，引入组件中之后，各个变量是相互独立的，值的修改在组件中不会相互影响。

# mixin 的缺点

1.  引入的组件中，变量名不能相同，否则会覆盖。
2.  滥用的话后期很难维护
3.  不好追溯源，排查问题稍显麻烦
4.  不能轻易的重复代码
