---
title: fetchApi
date: 2021-08-02 17:07:55
categories: Javascript
tags:
    - javascript
    - fetch
---

# fetch Api

XMLHttpRequest 的升级版，用于在 JavaScript 脚本里面发出 HTTP 请求。目前除了ie,主流浏览器都已兼容.

## 用法

1. 简单用法

```javascript
fetch('http://example.com/movies.json')
  .then(function(response) {
    return response.json();
  })
  .then(function(myJson) {
    console.log(myJson);
  });

```

Promise 可以使用 await 语法改写，使得语义更清晰。

```
async function getJSON() {
  let url = 'http://example.com/movies.json';
  try {
    let response = await fetch(url);
    return await response.json();
  } catch (error) {
    console.log('Request Failed', error);
  }
}

```

2. 上传 JSON 数据

```
var url = 'https://example.com/profile';
var data = {username: 'example'};

fetch(url, {
  method: 'POST', // or 'PUT'
  body: JSON.stringify(data), // data can be `string` or {object}!
  headers: new Headers({
    'Content-Type': 'application/json'
  })
}).then(res => res.json())
.catch(error => console.error('Error:', error))
.then(response => console.log('Success:', response));

```

3. 带参数用法

```javascript

postData('http://example.com/answer', {answer: 42})
  .then(data => console.log(data)) // JSON from `response.json()` call
  .catch(error => console.error(error))

function postData(url, data) {
  return fetch(url, {
    body: JSON.stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, *omit
    headers: {
      'user-agent': 'Mozilla/4.0 MDN Example',
      'content-type': 'application/json'
    },
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, cors, *same-origin
    redirect: 'follow', // manual, *follow, error
    referrer: 'no-referrer', // *client, no-referrer
  })
  .then(response => response.json()) // parses response to JSON
}

```

4. 上传文件

```javascript
var formData = new FormData();
var fileField = document.querySelector("input[type='file']");

formData.append('username', 'abc123');
formData.append('avatar', fileField.files[0]);

fetch('https://example.com/profile/avatar', {
  method: 'PUT',
  body: formData
})
.then(response => response.json())
.catch(error => console.error('Error:', error))
.then(response => console.log('Success:', response));

```

5. 上传多个文件

```javascript
var formData = new FormData();
var photos = document.querySelector("input[type='file'][multiple]");

formData.append('title', 'My Vegas Vacation');
// formData 只接受文件、Blob 或字符串，不能直接传递数组，所以必须循环嵌入
for (let i = 0; i < photos.files.length; i++) {
    formData.append('photo', photos.files[i]);
}

fetch('https://example.com/posts', {
  method: 'POST',
  body: formData
})
.then(response => response.json())
.then(response => console.log('Success:', JSON.stringify(response)))
.catch(error => console.error('Error:', error));
```

6. response

fetch()请求成功以后，得到的是一个 Response 对象。它对应服务器的 HTTP 回应。

```
const response = await fetch(url);
```

前面说过，Response 包含的数据通过 Stream 接口异步读取，但是它还包含一些同步属性，对应 HTTP 回应的标头信息（Headers），可以立即读取。

```

async function fetchText() {
  let response = await fetch('/readme.txt');
  console.log(response.status);
  console.log(response.statusText);
}

```

- Response.ok

Response.ok属性返回一个布尔值，表示请求是否成功，true对应 HTTP 请求的状态码 200 到 299，false对应其他的状态码。

- Response.status

Response.status属性返回一个数字，表示 HTTP 回应的状态码（例如200，表示成功请求）。

- Response.statusText

Response.statusText属性返回一个字符串，表示 HTTP 回应的状态信息（例如请求成功以后，服务器返回"OK"）。

- Response.url

Response.url属性返回请求的 URL。如果 URL 存在跳转，该属性返回的是最终 URL。

- Response.type

Response.type属性返回请求的类型。可能的值如下：

    - basic：普通请求，即同源请求。
    - cors：跨域请求。
    - error：网络错误，主要用于 Service Worker。
    - opaque：如果fetch()请求的type属性设为no-cors，就会返回这个值，详见请求部分。表示发出的是简单的跨域请求，类似<form>表单的那种跨域请求。
    - opaqueredirect：如果fetch()请求的redirect属性设为manual，就会返回这个值，详见请求部分。

- Response.redirected

Response.redirected属性返回一个布尔值，表示请求是否发生过跳转。


7. body

Body 类定义了以下方法（这些方法都被 Request 和Response所实现）以获取 body 内容。这些方法都会返回一个被解析后的Promise (en-US)对象和数据。

- [ArrayBuffer()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
    - 得到二进制 ArrayBuffer 对象。主要用于获取流媒体文件
- [blob()](https://developer.mozilla.org/en-US/docs/Web/API/Response/blob)
    - 得到二进制 Blob 对象。用于获取图片等二进制文件。
- [json() (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/Response/json)
    - 得到 JSON 对象。
- [text() (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/Response/text)
    - 得到文本字符串。比如 HTML 文件。
- [formData() (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/Response/formData)
    - 得到 FormData 表单对象。

==上面5个读取方法都是异步的，返回的都是 Promise 对象。必须等到异步操作结束，才能得到服务器返回的完整数据。==


```javascript
var form = new FormData(document.getElementById('login-form'));
fetch("/login", {
  method: "POST",
  body: form
})

```

8. Polyfill

如果要在不支持的浏览器中使用 Fetch，可以使用 [Fetch Polyfill](https://github.com/github/fetch)。

### 引用

> [阮一峰博客](https://www.ruanyifeng.com/blog/2020/12/fetch-tutorial.html)
> [mozilla](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch)

