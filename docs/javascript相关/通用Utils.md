# 项目通用方法

> 判断是否是对象

```js
export const isObject = (value) => {
  if (value === null) return false; // 显式处理null值
  const constructorProperty = value.constructor;
  return (
    value &&
    typeof value === "object" &&
    constructorProperty === Object &&
    Object.prototype.toString.call(value) === "[object Object]"
  ); // 使用更健壮的方法进行检查
};
```

> Promise 延迟函数

```js
export default (timeout) =>
  new Promise((resolve) => setTimeout(resolve, timeout));
```

> Promise 失败重连函数

该函数用于重试指定的异步函数 fn，最多重试 retries 次，每次重试之间等待 time 毫秒的延迟。如果函数执行成功，则直接返回结果；如果执行失败，则捕获错误，等待指定时间后进行下一次重试。如果已经重试了 retries 次仍然失败，则抛出错误。

```js
import delay from "./promiseDelay";

/**
 * @param {Function} fn
 * @param {Number} retries
 * @param {Number} time
 */
const retry = (fn, retries, time) =>
  fn().catch((err) => {
    return delay(time).then(() =>
      retries > 1 ? retry(fn, retries - 1, time) : Promise.reject(err)
    );
  });

export default retry;
```
