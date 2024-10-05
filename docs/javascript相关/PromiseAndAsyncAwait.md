# ES6 Promise

## 前言

ES6 Promise 是一个异步编程解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6 将其写进了语言标准，统一了用法，原生提供了 Promise 对象。

ES6 Promise 有三种状态：

- `pending`：初始状态， neither fulfilled nor rejected
- `fulfilled`：成功状态，resolved（这里跟成功回调函数名冲突了，所以用 fulfilled）
- `rejected`：失败状态，rejected

Promise 对象代表一个异步操作，有三种状态：pending（进行中）、fulfilled（已成功）和 rejected（已失败）。Promise 对象有以下两个特点：

1. 对象的状态不受外界影响。Promise 对象代表一个异步操作，有三种状态：pending（进行中）、fulfilled（已成功）和 rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是 Promise 对象这个名字的由来，它的英语意思就是“承诺”

2. `一旦状态改变，就不会再变`，任何时候都可以得到这个结果。Promise 对象的状态改变，`只有两种可能：从 pending 变为 fulfilled 和从 pending 变为 rejected`。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为 resolved（已定型）。如果改变已经发生了，你再对 Promise 对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。

## Promise 对象的用法

**_定义一个 Promise 对象，`new Promise()`函数的参数是一个函数，该函数的两个参数分别是`resolve`和`reject`。它们是 Promise 对象的两个函数。resolve 和 reject 函数是异步代码执行的关键。_**

1. resolve 处理成功时的回调函数，reject 处理失败时的回调函数。
2. then 方法 是异步操作成功时调用，then 方法的第一个参数是 resolve 函数，异步操作成功时调用，then 方法的第二个参数是 reject 函数，异步操作失败时调用。
3. catch 方法 是异步操作失败时调用。
4. finally 方法 是异步操作完成时调用。

```js
function getJSON(url) {
  let promise = new Promise(function (resolve, reject) {
    let xhr = new XMLHttpRequest()
    xhr.open('GET', url, true)
    xhr.onload = function () {
        if (xhr.status === 200) {
            resolve(xhr.responseText)
        } else {
            reject(new Error(xhr.statusText))
        }
    }

  })

  return promise
}

// 处理方式1 链式写法
getJSON('https://api.github.com/users/github').then((res)=>{
    // 处理成功
    console.log(res)
}).catch((err)=>{
    // 处理失败
    console.error(err)
}).finally(()=>{
    // 无论状态如何都会执行
    console.log('finally')
})


// 处理方式2
getJSON('https://api.github.com/users/github').then((res)=>{
    console.log(res)
},(err)=>{
    console.error(err)
}).finally(()=>{
    console.log('finally')
})

// async await
async function getJSONData(){
    try {
      const result = await getJSON('https://api.github.com/users/github')
    }catch(err){
         console.error(err)
    } finally{ {
         console.log('finally')
    }
}
```

## async 和 await 在干什么

async 和 await 是 ES7 引入的新语法，用来简化 Promise 的写法。async 函数返回一个 Promise 对象，await 语句用来获取 Promise 对象的值。

**async 用于申明一个 function 是异步的，而 await 用于等待一个异步方法执行完成**

#### async 起什么作用

**async 函数返回一个 Promise 对象，async 函数内部 return 的不是 Promise 对象，而是直接返回一个值，这个值会被 Promise 对象的 resolve 方法解析。**

```js
async function testAsync() {
  return 'hello async'
}

// 输出 返回 Promise { 'hello async' }
const result = testAsync()
// 执行
result.then(res => {
    console.log(res) -> hello async
})
```

> 抛出异常

```js
async function testAsync() {
  throw new Error('出错了')
  return 'hello async'
}
// 链式写法
testAsync()
  .then(res => {
    // 这里不会执行
    console.log('resolved', res)
  })
  .catch(err => {
    // 执行 rejected Error: 出错了
    console.log('rejected', err)
  })

// 或者 try catch 捕获
```
