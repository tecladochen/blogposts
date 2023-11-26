> _AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。_

AJAX 最大的优点是在**不重新加载整个页面的情况下，可以与服务器交换数据并更新部分网页内容**。
原生 AJAX 使用 xhr 对象发起异步请求，而 axios 则是一个支持 Promise 的底层还是基于 xhr 对象的封装库，而 fetch 则是一个使用 Promise 的新的发起异步请求的 API。
## 基本使用
原生 Ajax 就是浏览器提供的 XMLHttpRequest 接口对象来创建 xhr 对象来发起异步请求。
原生 Ajax 的创建步骤是：

1. 创建一个 xhr 对象
2. 监听 xhr 的 onreadystatechange 事件，判断 Ajax 的状态码
3. 判断 HTTP 的响应状态码
4. xhr.open() 准备发送请求
5. xhr.send() 发送请求
> xhr.send() 发送时可以接受一个对象或字符串作为 POST 请求的数据体，在发送 GET 请求时不接受参数。

```javascript
const xhr = new XMLHttpRequest();
xhr.onreadystatechange = () => {
   if (xhr.readyState !== 4) return;
   if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
       console.log(xhr.responseText);
    }
};
xhr.open('GET', 'https://www.baidu.com', true);
xhr.send(null);
```
通过监听 onreadystatechange 事件执行判断，当 Ajax 状态码为 4 时，表示已接受全部响应数据，可以正常使用，再判断 http 状态码，如果在 [200, 300) 之间表示 http 正确响应，如果是 304 表示访问内容没变化使用缓存数据。

| Ajax 状态码 | 状态 |
| --- | --- |
| 0 | （**未初始化**）未启动 |
| 1 | （**启动**）已经调用 open()，但尚未调用 send() |
| 2 | （**发送**）发送状态，已经调用 send()，但尚未接收到响应 |
| 3 | （**接收**）已经接收到部分响应数据 |
| 4 | （**完成**）已经接收到全部响应数据，而且已经可以在浏览器中使用了 |

考虑到 xhr 还有 load 事件可以在 Ajax 状态码等于 4 时触发，所以原生 Ajax 还有另一种写法。这种写法可以帮我们少写一次判断，以及有很好的兼容性。
```javascript
const xhr = new XMLHttpRequest();
xhr.addEventListener('load', () => {
	if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
    console.log(xhr.response);
  }
}, false);
xhr.open('GET', 'https://www.baidu.com', true);
xhr.send(null);
```
如果需要支持 Promise ，也可以通过函数封装 ajax 请求返回一个 Promise 对象。
```javascript
const ajax = (url) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.addEventListener(
      "load",
      () => {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
          resolve(xhr.response);
        } else {
          reject(new Error("Response Error"));
        }
      },
      false
    );
    xhr.open("GET", url, true);
    xhr.send(null);
  });
};
```
## XHR 对象
原生 ajax 通过 xhr 对象来实现，xhr 对象上有许多常用的属性、方法和事件。
### 实例属性
**_xhr.readyState_**
返回一个整数，表示当前请求状态，即状态码（上面提过）。
**_xhr.response_**
该属性只读，表示服务器返回的数据体，可能是任何类型。
**_xhr.responseType_**
该属性可写，告诉浏览器如何解读返回数据的类型。
**_xhr.timeout_**
设置超时时间，超时后触发 ontimeout 事件。
### 实例方法
**_xhr.setRequestHeader()_**
该方法用于设置浏览器发送 Http 请求的头信息，而且必须在 open 之后，send 之前调用。
> 方法调用接受俩个参数，第一个是头信息的字段名，第二个是头信息的字段值，该方法可以多次调用，一次只能设置一条字段。

```javascript
xhr.setRequestHeader('Content-Type', 'application/json');
xhr.setRequestHeader('Content-Length', JSON.stringify(data).length);
xhr.send(JSON.stringify(data));
```
**_xhr.abort()_**
该方法用来终止已经发送的 Http 请求。
### 实例事件
**_xhr.onreadySteateChange_**
当 readyState 属性改变时，就会触发 readyStateChange 事件，一般通过给 onreadyStateChange 属性指定一个监听函数来对不同状态进行不同处理。
**_load、error、abort、timeout _**
load 事件表示服务器传来的数据接收完毕；error 事件表示请求出错；abort 事件表示请求被中断；timeout 事件表示请求超时。
