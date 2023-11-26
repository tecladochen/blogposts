ESM 模块化标准的推出结束了 JavaScript 没有提供语言级模块化能力的时代，以及 ESM 模块化标准还带来更为强大的模块化能力，使得前后端开发模块标准走向统一。在服务器端，以往都是使用 CommonJS 模块规范，但是现在 ESM 标准在性能和功能都要比其更优秀。
## 导出模块
在 ESM 标准中，一个 JavaScript 文件就是一个模块，模块之间相互独立，外部无法访问内部内容，如果希望外部能够获取某些变量则需要使用导出语句。
导出语句使用 export 关键字，可以通过 export + 声明 来导出变量。
```javascript
export const name = 'jack'
export const age = 18
```
上面这种写法，在声明前加 export 关键字的写法等价于下面这种写法，但是更推荐使用下面这种写法，因为可以在脚本底部很快看出导出哪些变量。在导出时可以使用别名这样在导入时也只能用别名来获取。
```javascript
const name = 'jack'

export { name }
export { name as nickname }  // 使用别名
```
一个模块可以有一个默认导出语句，默认导出可以配合默认导入来使用，使用者不用关注内部变量名称就可以直接使用更为方便。
```javascript
// 默认导出一个对象
export default {
  name: 'jack',
  age: 18
}
```
## 导入模块
在 ESM 规范中，导入语句要指定完整路径，不能省略文件扩展名。在导入默认值时是不使用大括号的，而具名导入是必须写在大括号里的，在导入时也支持别名。
```javascript
import user from './module.js'  // 默认导入
import { name, age } from './module.js'  // 具名导入
import user, { name, age } from './module.js'  // 同时导入默认和具名变量
import { name as nickname } from './module.js'
```
对于具名导入全部变量有简单的写法来避免代码过于冗长：
```javascript
import * as user from './module.js'
```
下面俩种写法是等价的，表示执行一次导入文件的代码。
```javascript
import {} from './module.js'
import './module.js'
```
ESM 标准是在 2015 年随 ES6 发布的 Module 标准，在前端和后端都可以通用，Node 默认不支持 ESM 标准的模块，需要做一些配置才能使用。主要是俩种方式：

1. 使用 .mjs 文件扩展名代替 .js 扩展名，提示 Node 该文件使用 ESM 标准。
2. 不用修改文件扩展名，在 package.json 文件中加上 `"type" : "module"`配置项即可。
## 互相引用
在新的 Node 项目更推荐使用 ESM 标准的模块来构建项目，因为它更加高效而且社区生态也在往 ESM 标准方向发展。另外，在 ESM 模块中支持导入 CommonJS 模块，但是 CommonJS 并不支持导入 ESM 模块，所以使用 ESM 模块可以过渡和兼容更多的模块。
在 ESM 模块中，可以使用 import 语句导入 CommonJS 模块，但是只能导入一个默认成员。
```javascript
// CommonJS 模块
module.exports = {
  name: 'jack',
  sayHello: () => console.log('hello')
}

// ES Module 模块
import user from './common.js'
```
需要注意的是，import 语句并不支持解构，只能默认导入 CommonJS 模块，导入的内容就是 module.exports 所赋值的内容。








