Fetch 是浏览器提供的新的 API 来替代 XMLHttpRequest，Fetch 的功能基本和 XMLHttpRequest 相同，但是有三个主要的升级：

1. Fetch 采用 ES 标准的 Promise，不使用回调函数。
2. 采用模块化设计，API 分散在多个模块中，包括 Response、Request、Headers 等。
3. 采用数据流处理数据，可以分块读取，有利于提高网站性能，减少内存占用。

在用法上，Fetch 接受一个 url 字符作为参数，默认向该地址发出 GET 请求，返回一个 Promise 对象。
```javascript
fetch('https://api.github.com/users/ruanyf')
  .then(response => response.json())
  .then(json => console.log(json))
  .catch(err => console.log('Request Failed', err)); 
```
Promise 可以使用 async 函数写法，使得语义更加清晰。
```javascript
async function getJSON() {
  let url = 'https://api.github.com/users/ruanyf';
  try {
    let response = await fetch(url);
    return await response.json();
  } catch (error) {
    console.log('Request Failed', error);
  }
}
```
## Response 对象
Fetch 请求成功后，得到一个 Response 对象，它对应服务器的 Http 回应。
Response 包含的数据通过 Stream 接口异步读取，但是它还包括一些同步属性可以立即读取。
常用的 Response 同步属性：
**Response.ok**
返回一个布尔值，表示请求是否成功, `true`对应 HTTP 请求的状态码 200 到 299, `false`对应其他的状态码。
**Response.status**
返回一个数字，表示 HTTP 回应的状态码（例如200，表示成功请求）。
**Response.statusText**
返回一个字符串，表示 HTTP 回应的状态信息（例如请求成功以后，服务器返回“OK”）。
**Response.url**
返回请求的 URL。如果 URL 存在跳转，该属性返回的是最终 URL。
**Response.redirected**
返回一个布尔值，表示请求是否发生过跳转。
**Response.type**
返回请求的类型。可能的值如下：`basic`同源请求；`cors`跨域请求；`error`网络错误。
> 注意：Fetch 只有在网络错误或无法连接时报错，其他错误都认为请求成功。所以判断请求是否真正成功，需要通过条件判断得出。

```javascript
async function fetchText() {
  let response = await fetch('/readme.txt');
  if (response.ok) {
    return await response.text();
  } else {
    throw new Error(response.statusText);
  }
}
```
Response 对象根据服务器返回的不同类型的数据，提供了不同的读取方法，这些方法都是异步的，返回都是 Promise 对象。必须等异步操作结束，才能得到服务器返回的完整数据。
> **response.text()**：得到文本字符串。
> **response.json()**：得到 JSON 对象。
> **response.blob()**：得到二进制 Blob 对象。
> **response.formData()**：得到 FormData 表单对象。
> **response.arrayBuffer()**：得到二进制 ArrayBuffer 对象。

Response 对象的数据是 Stream 对象，它只允许读取一次，意味上面的方法只能调用一次，获取到数据就不能再次调用，所以，当需要多次读取需要使用 `Response.clone()`方法。
```javascript
const response1 = await fetch('flowers.jpg');
const response2 = response1.clone();

const myBlob1 = await response1.blob();
const myBlob2 = await response2.blob();
```
Response 对象还暴露了一个底层的接口，就是`Response.body`属性，它返回一个 ReadableStream 对象，可以让用户分块读取内容。
## Headers 对象
Response 对象还有一个`Response.headers`属性，指向一个 Headers 对象，Headers 对象对应所有 Http 标头，Headers 对象提供很多方法：
> **Headers.get()**：根据指定的键名，返回键值。
> **Headers.set()**：将指定的键名设置为新的键值，如果该键名不存在则会添加。
> **Headers.has()**： 返回一个布尔值，表示是否包含某个标头。
> **Headers.append()**：添加标头。
> **Headers.delete()**：删除标头。
> **Headers.keys()**：返回一个遍历器，可以依次遍历所有键名。
> **Headers.values()**：返回一个遍历器，可以依次遍历所有键值。
> **Headers.entries()**：返回一个遍历器，可以依次遍历所有键值对`[key, value]`。
> **Headers.forEach()**：依次遍历标头，每个标头都会执行一次参数函数。

这些方法中，最常用的是 `response.headers.get()`，用于读取某个标头的值。
```javascript
let response =  await  fetch(url);
response.headers.get('Content-Type')
// application/json; charset=utf-8
```
## Request 对象
Fetch 的第一个参数就是 URL，它还可以接受第二个参数，作为配置对象，定制发送的 Http 请求。
```javascript
const response = await fetch(url, {
  method: 'POST',
  headers: {
    "Content-type": "application/x-www-form-urlencoded; charset=UTF-8",
  },
  body: 'foo=bar&lorem=ipsum',
});

const json = await response.json();
```
配置对象用到了三个属性：
> **method**：Http 请求的方法，POST，DELETE，PUT 等。
> **headers**：一个对象用来定制 Http 请求的标头。
> **body**：POST 请求的数据体。

如果提交 JSON 格式的数据，需要设置标头中的 `Content-Type`值。
```javascript
const user =  { name:  'John', surname:  'Smith'  };
const response = await fetch('/article/fetch/post/user', {
  method: 'POST',
  headers: {
   'Content-Type': 'application/json;charset=utf-8'
  }, 
  body: JSON.stringify(user) 
});
```
因为 Fetch 请求的底层用的是 Request 对象的接口，所以在配置对象中与 Request 对象的属性一样。
下面是 Fetch 配置对象完整形式：
```javascript
const response = fetch(url, {
  method: "GET",
  headers: {
    "Content-Type": "text/plain;charset=UTF-8"
  },
  body: undefined,
  referrer: "about:client",
  referrerPolicy: "no-referrer-when-downgrade",
  mode: "cors",
  credentials: "same-origin",
  cache: "default",
  redirect: "follow",
  integrity: "",
  keepalive: false,
  signal: undefined
});
```
在 Fetch 请求发送后，如果中途想要取消，需要使用`AbortController`对象。
首先新建 `AbortController` 实例，然后再 Fetch 请求中配置 `signal`属性等于 `AbortController` 实例生成的信号 `AbortController.signal` 。`controller.abort()`方法用于发出取消信号，这时 Fetch 就会被取消，也可以通过`controller.signal.aborted`属性判断取消信号有没有发出。
```javascript
let controller = new AbortController();
let signal = controller.signal;

fetch(url, {
  signal: controller.signal
});

signal.addEventListener('abort',
  () => console.log('abort!')
);

controller.abort(); // 取消

console.log(signal.aborted); // true
```
