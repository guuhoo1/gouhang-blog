## websorket

websorket 是一种用于在浏览器和服务器之间建立持久连接的技术。

### 封装一个 websorket 类

> 包括常用的方法 心跳检测 重连 等

```js
/**
 * WebSocket客户端类，用于建立和管理WebSocket连接。
 * 提供连接、发送消息、监听事件、重新连接等功能。
 */
class WebSocketClient {
  /**
   * 构造函数初始化WebSocket连接的参数。
   * @param {string} url WebSocket服务器的URL。
   * @param {string[]} [protocols] 可选的协议数组。
   * @param {Object} options 配置选项，包括重连间隔和心跳间隔。
   */
  constructor(url, protocols, options) {
    this.url = url;
    this.protocols = protocols || [];
    this.socket = null;
    this.callbacks = {
      onopen: [],
      onmessage: [],
      onclose: [],
      onerror: [],
    };
    this.reconnectInterval = options.reconnectInterval || 3000;
    this.pingInterval = options.pingInterval || 20000;
    this.isReconnecting = false;
    this.pingTimeoutId = null;
    this.timeout = null;
  }

  /**
   * 建立WebSocket连接，如果已建立则直接返回。
   * @returns {Promise} 返回一个Promise对象，解析为WebSocket实例。
   */
  connect() {
    if (this.socket && this.socket.readyState === WebSocket.OPEN) {
      return Promise.resolve(this.socket);
    }

    return new Promise((resolve, reject) => {
      this.socket = new WebSocket(this.url, this.protocols);

      this.socket.onopen = (event) => {
        this.callbacks.onopen.forEach((callback) => callback(event));
        this.startPing();
        resolve(this.socket);
      };

      this.socket.onmessage = (event) => {
        this.callbacks.onmessage.forEach((callback) => callback(event));
        this.handlePong();
      };

      this.socket.onclose = (event) => {
        this.callbacks.onclose.forEach((callback) => callback(event));
        this.stopPing();
        this.reconnect();
      };

      this.socket.onerror = (event) => {
        this.callbacks.onerror.forEach((callback) => callback(event));
        this.stopPing();
        this.reconnect();
        reject(event);
      };
    });
  }

  /**
   * 尝试重新连接WebSocket服务器。
   */
  reconnect() {
    //没连接上会一直重连，设置延迟避免请求过多
    if (this.isReconnecting) return;
    this.isReconnecting = true;
    console.log("WebSocket disconnected. Attempting to reconnect...");
    if (this.timeout) {
      clearTimeout(this.timeout);
    }
    this.timeout = setTimeout(() => {
      this.socket = null;
      this.connect()
        .then(() => {
          this.isReconnecting = false;
        })
        .catch(() => {
          this.isReconnecting = false;
          this.reconnect(); // 如果连接失败，再次尝试重连
        });
    }, this.reconnectInterval);
  }

  /**
   * 开始发送心跳包。
   */
  startPing() {
    this.pingTimeoutId = setInterval(() => {
      if (this.socket.readyState == 1) {
        this.send(JSON.stringify({ type: "ping" }));
      } else {
        clearTimeout(this.pingTimeoutId);
        this.reconnect();
      }
    }, this.pingInterval);
  }

  /**
   * 处理收到的心跳响应 pong 消息。
   */
  handlePong() {
    // 在接收到pong消息时，可以在这里做些事情，比如重置心跳超时计时器
  }

  /**
   * 停止发送心跳包。
   */
  stopPing() {
    clearInterval(this.pingTimeoutId);
  }

  /**
   * 发送消息到WebSocket服务器。
   * @param {string} data 要发送的数据，通常为JSON字符串。
   * @throws 如果WebSocket连接未打开，则抛出错误。
   */
  send(data) {
    if (this.socket && this.socket.readyState === WebSocket.OPEN) {
      this.socket.send(data);
    } else {
      // throw new Error("WebSocket connection is not open.");
      this.reconnect();
    }
  }

  /**
   * 关闭WebSocket连接。
   * @param {number} [code] 可选的关闭代码。
   * @param {string} [reason] 可选的关闭原因。
   */
  close(code, reason) {
    if (this.socket) {
      this.socket.close(code, reason);
    }
  }

  /**
   * 添加事件监听器。
   * @param {string} event 要监听的事件类型。
   * @param {Function} callback 事件触发时调用的回调函数。
   * @throws 如果事件类型不受支持，则抛出错误。
   */
  addEventListener(event, callback) {
    if (this.callbacks[event]) {
      this.callbacks[event].push(callback);
    } else {
      throw new Error(`Event '${event}' is not supported.`);
    }
  }

  /**
   * 移除事件监听器。
   * @param {string} event 要移除监听器的事件类型。
   * @param {Function} callback 要移除的回调函数。
   * @throws 如果事件类型不受支持，则抛出错误。
   */
  removeEventListener(event, callback) {
    if (this.callbacks[event]) {
      const index = this.callbacks[event].indexOf(callback);
      if (index !== -1) {
        this.callbacks[event].splice(index, 1);
      }
    } else {
      throw new Error(`Event '${event}' is not supported.`);
    }
  }
}
```

## webSorket.js 具体使用方法

```js
const wsClient = new WebSocketClient("wss://example.com/ws", [], {
  reconnectInterval: 5000, // 自定义重连间隔时间为5秒
  pingInterval: 15000, // 自定义心跳间隔时间为15秒
});

wsClient.addEventListener("onopen", (event) => {
  console.log("Connection opened:", event);
});

wsClient.addEventListener("onmessage", (event) => {
  console.log("Message received:", event.data);
  if (event.data.type === "pong") {
    // 处理pong消息，例如重置心跳超时计时器
  }
});

wsClient
  .connect()
  .then((socket) => {
    console.log("Connected to WebSocket:", socket);
    wsClient.send("Hello Server!");
  })
  .catch((error) => {
    console.error("Error connecting to WebSocket:", error);
  });
```
