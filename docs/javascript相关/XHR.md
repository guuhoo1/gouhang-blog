### 介绍 XHR

XHR 是 XMLHttpRequest 的简称，是浏览器提供的一个对象，用于向服务器发送请求，获取服务器返回的数据。

#### 封装 XHR 类方法

```js
// xhr封装请求方法

/**
 * 表示一个XMLHttpRequest实例，封装了发送XMLHttpRequest的功能。
 */
class XhrRequestInstance {
  /**
   * 构造一个XMLHttpRequest实例。
   * @param {Object} config - 配置对象，包含url、method和timeout属性。
   */
  constructor(config) {
    this.xhr = new XMLHttpRequest()
    this.url = config.url
    this.method = config.method || 'GET'
    this.timeout = config.timeout || 5000
    this.timer = null
  }

  /**
   * 将参数对象转换为URL查询字符串格式。
   * @param {Object} params - 要转换的参数对象。
   * @returns {String} 转换后的URL查询字符串。
   */
  getParams(params) {
    return Object.entries(params)
      .map(item => {
        return `${item[0]}=${item[1]}`
      })
      .join('')
  }

  /**
   * 发送一个XMLHttpRequest。
   * @param {Object} params - 请求参数（如果有）。
   * @returns {Promise} 返回一个Promise对象，成功时解析响应，失败时拒绝错误信息。
   */
  request(params = {}) {
    return new Promise((resolve, reject) => {
      clearTimeout(this.timer)
      this.timer = setTimeout(() => {
        this.xhr.abort()
        reject('请求超时')
      }, this.timeout)

      if (this.method === 'GET') {
        params = this.getParams(params)
        this.xhr.open(this.method, `${this.url}${params ? `?${params}` : ''}`)
      } else {
        this.xhr.setRequestHeader('Content-Type', 'application/json')
        this.xhr.open(this.method, this.url)
        params = JSON.stringify(params)
      }

      this.xhr.onload = () => {
        if (this.xhr.status === 200) {
          clearTimeout(this.timer)
          resolve(this.xhr.response)
        } else {
          clearTimeout(this.timer)
          reject(this.xhr.response)
        }
      }

      this.xhr.onerror = () => {
        reject(this.xhr.response)
      }

      this.xhr.send(this.method === 'POST' && params)
    })
  }
}

export default XhrRequestInstance
```
