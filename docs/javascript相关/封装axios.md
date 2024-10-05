# 封装 axios

> 下载 axios 依赖

`npm install axios -S`

> CancelToken.js

[ES6 Map 相关知识](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map)

```js
// 紀錄請求中 API
export const pendingRequests = new Map();

export function removeRequestPending(config) {
  const uuid = generateUUID(config); // 保持生成UUID的逻辑不变
  const item = pendingRequests.get(uuid);
  if (item) {
    item.cancel();
    pendingRequests.delete(uuid);
  }
}

// 优化后的取消所有请求的函数
export function cancelAllPending() {
  for (const item of pendingRequests.values()) {
    item.cancel();
  }
  pendingRequests.clear();
}

// 优化后的清除特定请求的函数
export function clearRequestPending(config) {
  const uuid = generateUUID(config); // 保持生成UUID的逻辑不变
  pendingRequests.delete(uuid);
}

// 优化后的添加请求到管理器的函数
export function addRequestPending(item) {
  pendingRequests.set(item.uuid, item);
}

// 辅助函数，生成用于识别请求的UUID
export function generateUUID(config) {
  return `${config.url}&${config.method}${JSON.stringify(config.data)}`;
}
```

> index.js

```js
import axios from "axios";
import {
  removeRequestPending,
  addRequestPending,
  clearRequestPending,
  generateUUID
} from "./CancelToken";

const defaultConfig = {
  timeOut: 50000,
  baseURL: import.meta.env.VITE_BASE_URL
    ? import.meta.env.VITE_BASE_URL
    : "http://110.42.184.111",
};

const Cancel = axios.CancelToken;

const axiosInstance = axios.create(defaultConfig);

// 請求攔截
axiosInstance.interceptors.request.use(
  (config) => {
    console.log("請求攔截測試");
    removeRequestPending(config);

    config.cancelToken = new Cancel((cancel) => {
      const requestItem = {
        uuid: generateUUID(config)
        cancel,
      };
      addRequestPending(config);
    });

    return config;
  },
  (err) => {
    return Promise.reject(err);
  }
);

// 回應攔截
axiosInstance.interceptors.response.use(
  (res) => {
    console.log("回應攔截測試");
    clearRequestPending(res.config);

    return res;
  },
  (err) => {
    clearRequestPending(err.config);
    return Promise.reject(err);
  }
);

export const httpRequestGet = (url, params) => {
  return axiosInstance
    .get(url, params)
    .then((res) => res.data)
    .catch();
};

export const httpRequestPost = (url, params) => {
  return axiosInstance
    .post(url, params)
    .then((res) => res.data)
    .catch();
};
```
