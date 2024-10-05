# npm install 的不同选项：--save、--save-dev、-S、-D 的区别

## dependencies 和 devDependencies 的区别

- `dependencies`：生产环境依赖，即项目运行时需要依赖的包。

  - 比如 ramdajs 等。 运行时需要 ramdajs，所以需要安装到 dependencies 中。

- `devDependencies`：开发环境依赖，即项目开发时需要依赖的包。
  - 比如 scss 编译工具，在开发环境需要，在生产环境不需要，所以应该安装到 devDependencies 中。

## --save（-S）

--save（-S）选项会将依赖项添加到项目的 package.json 文件的 dependencies 属性中。

例如下载 express 模块，并添加到 dependencies 中。

`npm install express --save`

或者使用短选项 -S：

`npm install express -S`

安装完成后，package.json 文件中的 dependencies 字段将包含 Express 的信息：

```
"dependencies": {
  "express": "^4.17.1"
}
```

## --save-dev（-D）

--save-dev（-D）选项将模块添加到 devDependencies 中。

`--save-dev` 选项表示安装的依赖包是`开发环境`所需的，而不是生产环境。这意味着这些依赖包将被添加到 `package.json` 文件的 `devDependencies` 字段中，而不是 dependencies 字段。开发环境的依赖包通常包括`测试框架、构建工具`等，这些依赖包在生产环境中是不需要的。

例如，我们可以使用以下命令安装 sass 测试框架并将其添加到 devDependencies：

`npm install sass --save-dev`

或者使用短选项 -D：

`npm install sass -D`

安装完成后，package.json 文件中的 devDependencies 字段将包含 Mocha 的信息：

```js
"devDependencies": {
  "sass": "^8.4.0"
}
```

## 总结

总结一下，这些选项的区别如下：

- `--save`：将依赖项添加到 `package.json` 的 `dependencies` 中。
- `--save-dev`：将依赖项添加到 `package.json` 的 `devDependencies` 中。

在实际项目中，我们需要根据依赖包的用途来选择合适的选项。通常，对于应用程序运行时需要的依赖包，我们使用 --save 选项；对于开发、测试和构建过程中需要的依赖包，我们使用 --save-dev 选项。
