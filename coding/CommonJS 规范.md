在 ES6 的 ES Module 出现之前，CommonJS 一直是主流的模块化规范，虽然现在 ES Module 的使用越来越广泛，但由于历史原因，很多 Node.js 中使用的库都是以前以 CommonJS 规范实现的，完全更新换代也是需要时间的，所以在将来的较长一段时间里 CommonJS 还将一直存在，因此我们仍然有必要对 CommonJS 有一定的了解。
模块化实现了能够独立命名并单独完成一定功能的程序集合，CommonJS 规范通过将代码包装在匿名函数内，通过函数单独作用域的特性来模拟模块作用域。在模块内使用 module.exports 和 require 方法来实现内容的导出和导入。
## 导出模块
在 CommonJS 中，要导出变量通常有俩种形式，一种是向 module.exports 赋值一个对象，在对象中定义要导出的各项信息；另一种是通过 exports 新增属性的方式来导出内容。
```javascript
// 1. module.exports = { }
module.exports = {
  name: 'jack',
  age: 18
}

// 2. exports.prop
exports.name = 'jack'
exports.age = 18
```
## 导入模块
在 CommonJS 中使用 require 语句导入模块，并且模块被分为三大类：

1. Node.js 内置模块
2. 第三方模块
3. 本地自己的模块

在导入内置模块和第三方模块时不需要加路径，自定义的模块需要加路径。新版本的 Node 在导入内置模块时可以加上 node: 前缀来跳过模块缓存检查，直接加载原生模块。
```javascript
// 内置模块
const fs = require('node:fs')
// 第三方模块
const koa = require('koa')
// 相对路径文件模块
const foo = require('./foo.js')
```
在 Node.js 中默认使用 CommonJS 规范，当项目的 package.json 中的 type 值未指定或指定为 commonjs 时表示项目使用 CommonJS 规范，另外 .cjs 文件扩展名也表示该文件使用 CommonJS 规范。


