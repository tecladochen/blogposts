Axios 是一个基于 Promise 的网络请求库，同一套代码可以在浏览器和 node 环境中使用，在浏览器中使用 XMLHttpRequest 接口实现，在 node 中使用 http 模块实现。
## 基本使用
使用 Axios 发起一个 GET 请求，返回一个 Promise 对象。
```javascript
import axios from "axios";
axios.get('/users')
  .then(res => console.log(res.data))
  .catch(error => console.log(error));
```
Axios 也支持使用 async/await 语法来改写结构，这样看上去更清晰。
```javascript
async function getUser() {
  try {
    const response = await axios.get('/user?ID=12345');
    console.log(response);
  } catch (error) {
    console.error(error);
  }
}
```
## 请求方式
Axios 提供俩种发起请求的方式，一种是通过传递配置对象创建请求；另一种通过调用方法创建请求。
### 配置对象方式
**_axios(config)_**
通过传递配置对象方式发起一个 POST 请求
```javascript
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
});
```
**_axios(url[, config])_**
Axios 也支持将 URL 作为一个参数，把配置对象放在第二个参数作为可选项。
```javascript
// 发起一个 GET 请求 (默认请求方式)
axios('/user/12345');
```
### 调用方法方式
**_axios.get(url[, config])_**
**_axios.post(url[, data[, config]])_**
在调用方法来创建请求时，在配置中不需要指定 method， url， data这些属性。
```javascript
axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
  });
```
## 创建实例
**_axios.create([config])_**
可以直接使用 axios 对象来配置和发生请求，也可以通过 axios 创建多个的实例来封装不同请求的配置。
实例与 axios 对象的功能和使用方法一样。
```javascript
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```
## 请求配置
Axios 创建请求时有许多可选项，其中只有 URL 是必须的，如果没有指定 method，默认使用 GET 方法。
```javascript
{
  // url 是用于请求的服务器 URL
  url: '/user',

  // method 是创建请求时使用的方法
  method: 'get', // 默认值

  // baseURL 将自动加在 url 前面，除非 url 是一个绝对 URL。
  // 它可以通过设置一个 baseURL 便于为 axios 实例的方法传递相对 URL
  baseURL: 'https://some-domain.com/api/',

  // 自定义请求头
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // params 是与请求一起发送的 URL 参数
  // 必须是一个简单对象或 URLSearchParams 对象
  params: {
    ID: 12345
  },

  // data 是作为请求体被发送的数据
  // 仅适用 'PUT', 'POST', 'DELETE 和 'PATCH' 请求方法
  data: {
    firstName: 'Fred'
  },

  // timeout 指定请求超时的毫秒数。
  // 如果请求时间超过 timeout 的值，则请求会被中断
  timeout: 1000, // 默认值是 0 (永不超时)
}
```
## 响应结构
Axios 请求返回一个 response 响应对象，对象包括以下属性：
```javascript
{
  // `data` 由服务器提供的响应
  data: {},

  // `status` 来自服务器响应的 HTTP 状态码
  status: 200,

  // `statusText` 来自服务器响应的 HTTP 状态信息
  statusText: 'OK',

  // `headers` 是服务器响应头
  // 所有的 header 名称都是小写，而且可以使用方括号语法访问
  // 例如: `response.headers['content-type']`
  headers: {},

  // `config` 是 `axios` 请求的配置信息
  config: {},

  // `request` 是生成此响应的请求
  // 在node.js中它是最后一个ClientRequest实例 (in redirects)，
  // 在浏览器中则是 XMLHttpRequest 实例
  request: {}
}
```
## 默认配置
你可以给 Axios 或者其他实例指定默认配置，它将作用于每个请求。
```javascript
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```
```javascript
// 创建实例时配置默认值
const instance = axios.create({
  baseURL: 'https://api.example.com'
});

// 创建实例后修改默认值
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
```
配置会根据优先级进行合并，首先是 lib/default.js 库自带的默认值，然后是实例的 defaults 属性，最后是请求时的 config 参数，后面的优先级高于前面的。
```javascript
// 使用库提供的默认配置创建实例
// 此时超时配置的默认值是 `0`
const instance = axios.create();

// 重写库的超时默认值
// 现在，所有使用此实例的请求都将等待2.5秒，然后才会超时
instance.defaults.timeout = 2500;

// 重写此请求的超时时间，因为该请求需要很长时间
instance.get('/longRequest', {
  timeout: 5000
});
```
