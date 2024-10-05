### 封装一个上传类

```js
class fileInstance {
  constructor(config) {
    // 验证config参数
    if (!config || typeof config.accept !== "string") {
      throw new Error("Invalid configuration provided.");
    }
    this.accept = config.accept;
    this.inputDom = null;
    this.callbacks = {
      change: [],
    };
  }

  createInputElement() {
    return new Promise((resolve) => {
      // 如果已存在input元素，则重用，否则创建新的
      if (this.inputDom) {
        this.inputDom.value = ""; // 清空值以允许重新选择文件
        resolve();
        return;
      }

      const input = document.createElement("input");
      input.type = "file";
      input.accept = this.accept;
      input.style.display = "none";

      input.addEventListener("change", (e) => {
        const files = e.target.files;
        // 检查是否有文件被选择
        if (files.length > 0) {
          const file = files[0];
          this.callbacks.change.forEach((fn) => fn(file));
        }
        this.inputDom.value = ""; // 清空值以允许重新选择文件
      });

      this.inputDom = input;
      document.body.appendChild(input);
      resolve();
    });
  }

  click() {
    console.log("click");
    // 确保inputDom已经创建
    this.createInputElement().then(() => {
      if (this.inputDom) {
        this.inputDom.click();
      }
    });
  }

  addEventListener(event, callback) {
    if (this.callbacks[event]) {
      this.callbacks[event].push(callback);
    } else {
      // 使用Promise拒绝而不是抛出错误，以提供更灵活的错误处理
      return Promise.reject(new Error(`Event '${event}' is not supported.`));
    }
    return Promise.resolve();
  }

  removeInputDom() {
    if (this.inputDom) {
      document.body.removeChild(this.inputDom);
      this.inputDom = null;
      // 这里可以进一步清理callbacks中的引用，避免内存泄漏
      // 例如，如果有特定逻辑来解除这些引用
    }
  }

  changeInputAcceptAttrs(accept) {
    if (this.inputDom) {
      this.inputDom.setAttribute("accept", accept);
    }
  }
}
```

> 使用方法

```js
const btn = document.getElementById("btn");
const FileInstance = new fileInstance({
  accept: "image/*",
});

FileInstance.createInputElement();

FileInstance.addEventListener("change", (file) => {
  // console.log(file);
  const fileReader = new FileReader();
  fileReader.readAsDataURL(file);
  fileReader.onload = (e) => {
    const img = document.createElement("img");
    img.src = e.target.result;
    document.body.appendChild(img);
  };
});

btn.addEventListener("click", () => {
  FileInstance.click();
});
```
