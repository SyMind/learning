# webpack 更新历史

## webpack 2

### ES6 支持

不再需要在你的代码中翻译 import 和 export，webpack 现在支持解析这些语句。这使得可以利用 tree-shaking。

### tree-shaking

1. ES6 的模块引入是静态分析的，故而可以在编译时正确判断到底加载了什么代码。
2. 分析程序流，判断哪些变量未被使用、引用，进而删除代码。

babel
Person 类被包装成了一个 IIFE，然后返回一个构造函数。

UglifyJS
没有程序流分析，变量赋值就是有可能产生副作用的。

## webpack 3

### scope hoisting

webpack.optimize.ModuleConcatenationPlugin()

改进 JavaScript 在浏览器中的加载速度。

### 魔法注释

在使用 import 方法一步加载模块时，以注释的方式为 chunk 文件取别名。
webpack 懒加载使用内置的 SplitChunksPlugin 插件。

## webpack 4

* 默认情况下，使用 production 模式时，将自动并行化并缓存 UglifyJS 完成的压缩工作。
* 发布了新版本的插件系统，以便事件钩子和处理程序是单态的。
* 放弃对 Node v4 的支持。

### sideEffects

解决 tree-shaking 的问题，让 webpack 忽略掉副作用。

### 新特性

#### 支持如下模块类型
* javascript/auto
* javascript/esm
* javascript/dynamic
* json
* webassembly/experimental

#### 优化

* sideEffects: false
* webpack 自己移除死亡的分支
    * 以前：Uglify 移除死亡代码
    * 现在：webpack 移除死亡代码

#### 使用

CLI 已经移动到 webpack-cli

#### 性能

* UglifyJS 现在默认开启缓存、并行



# 启动过程

## compiler

Compiler 模块是 webpack 的支柱引擎，它通过 CLI 或 Node API 传递所有选项，创建出一个 compilation 实例。

## compilation

Compilation 模块会被 Compiler 用来创建新的编译（或新的构建）。compilation 实例能够访问所有的模块和它们的依赖（大部分是循环依赖）。它会对应应用程序的依赖图中所有模块进行字面上的编译（literal compilation）。在编译阶段，模块会被加载（loaded）、封存（sealed）、优化（optimized）、分块（chunked）、哈希（hashed）和重新创建（restored）。
