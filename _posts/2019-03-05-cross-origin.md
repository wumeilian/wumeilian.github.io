---
layout:     post
title:      "跨域的7种解决方案"
subtitle:   ""
author:     "wml"
header-img: "img/cross/1.jpg"
header-mask:  0.5
catalog: true
tags:

    - web
---

## 前言

`同源策略(Same-origin Policy)`,这个概念已经耳熟能详了,简单来说就是浏览器的一种安全策略,不允许浏览器中一个网站访问另一个网站的数据,除非这两个网站具有相同的`源(origin)`,即: 相同的协议(eg:http/https)、相同的主机地址（eg: www.a.com/www.b.com）、相同的端口号（www.a.com:3000/www.a.com:4000），只要有一项不同，都被认为是不同源而不被允许访问。

```js
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://test-circle.seeyouyima.com/v3/topic_detail?topic_id=36185682');
xhr.send();
xhr.onreadystatechange = function() {
    if(xhr.readyState == 4 && xhr.status == 200) {
        console.log(xhr.responseText);
    }else {
        console.log(xhr.statusText);
    }
}
```

这个是一个简单的get请求，本地发起请求，由于源不同，所以提示跨域，看下图，可以看出请求是成功的，只是服务器在没有特殊处理的情况下返回给浏览器时被浏览器拦截了，浏览器根据响应头`Access-Control-Allow-Origin`的值判断是否有权限获取数据。

![cross](/img/cross/2.jpg)

## 怎么解决跨域

同源策略的安全策略不置可否，但是有时候我们也希望不同源如和子域之间能进行数据交换，那如何绕过这一限制呢？业界已经有许多比较成熟的方法解决，都各有利弊，根据自己的情况选择。

### [document.domain](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/domain)

`document.domain`属性是最直接方法，在根域范围内，Mozilla允许吧domain属性设置它的上一级域，eg:

```js
var root = 'dev.a.com';
document.domain = 'a.com'; // right
document.domain = 'b.com'; // error
```

缺点也很明显，就是只限于父子域之间，甚至有些浏览器并不能设置改属性。

### 跨文档消息 [postMessage](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage)

> 语法：otherWindow.postMessage(message, targetOrigin, [transfer]);

`window.postMessage`是H5的API，通过它可以在不同域之间进行通信，不过要注意两点：

* window.postMessage()中的window始终是要通信的目标页面的window；
* 只能在iframe、window.open()的方式下通信

```html
<!-- pageA -->
<h1>Page A</h1>
<p id="txt"></p>
<button id="newWin">open new window</button>
<button id="postMsg">post msg to iframe</button>
<iframe id="receiverIframe" src="http://127.0.0.1:3000/pageB" frameborder="1" width="800" height="500"></iframe>

<script>
var test = {
    "code": 0,
    "name": "wml",
    "des": 'this page from pageA'
}

var openBtn = document.getElementById('newWin');
var postMsg = document.getElementById('postMsg');
var recIframe = document.getElementById('receiverIframe').contentWindow;
var text = document.getElementById('txt');

window.onload = function() {
    openBtn.addEventListener('click', openwindow);
    postMsg.addEventListener('click', sentMsg)
    window.addEventListener('message', reveiveMsg);
}

// window.open
function openwindow() {
    var target = 'http://127.0.0.1:3000/pageB'
    var pageB = window.open(target);
    //这里加setTImeout是因为需要等到PageB页面加载完成才能进行postMessage通信，但是在PageA内是无法对PageB进行onload事件监听，所以做延迟处理。
    setTimeout(function() {
        pageB.postMessage(test, target);
    }, 500)
}

function sentMsg() {
    recIframe.postMessage('This msg from PageA~', 'http://127.0.0.1:3000/pageB');
}

function reveiveMsg(event) {
    console.log(event, 'A: from B')
    text.innerHTML = event.data;
}
</script>
```

```html
<!-- page B -->
<h1 id="revMsg">Page B</h1>
<button id="postMsg">Post Message</button>

<script>
var revMsg = document.getElementById('revMsg');

window.onload = function() {  // 监听message，获取来自A页面的数据
    window.addEventListener("message", function(e) {
        console.log(e, 'B: from A');
        receiveMsg(e);
    }, false);

    postMsg.addEventListener('click', sendMsg)
    // window.addEventListener('message', receiveMessage, false);
}

function receiveMsg(event) {
    if(event.origin !== 'http://127.0.0.1:8083') return;

    revMsg.innerText = event.data.des ? `Messge from: ${event.data.des}, the name is ${event.data.name}` : `Messge from: ${event.data}`;
}

// 直接在 PageB (当前页面) 无法向 PageA 发送跨域信息
//  只有当 PageB (当前页面) 处于 PageA 页面内的 iframe 中的时候才能发送跨域信息
// otherWindow需要指向发送的窗口，即父级iframe，所以用top
function sendMsg() {
    top.postMessage('Hi, pageA, a message from pageB', 'http://127.0.0.1:8083')
}
</script>
```

pageB页面控制台输出：

![cross](/img/cross/3.jpg)

从pageB页面回传给pageA的数据

![cross](/img/cross/4.png)

该方法的缺点也是适用场景不普遍，只适合window.open和iframe出来的页面。

### jsonp

原理是因为`script`标签内的src属性能支持跨域请求，所以jsonp利用这点构建`script`标签，src属性为接口地址，通过这种做法向服务端请求json数据；

通过在服务端用一个回调函数把数据一起包裹起来并返回给客户端，然后客户端写好回调（处理数据），并动态创建一个script节点，通过src属性来调用服务端返回的回调函数。

```js
/*client*/
function jsonp({url, param, callback}) {
  return new Promise((resolve, reject) => {
    let script = document.createElement('script');
    script.setAttribute('type', 'text/javascript');
    window[callback] = function(data) {
      resolve(data);
      document.body.removeChild(script); // 调用完以后删除节点
    }

    // 拼接get参数
    param = {...param, callback};
    let query = [];
    for(let i in param) {
      query.push(`${i}=${param[i]}`);
    }

    script.src = `${url}?${query.join('&')}`;
    document.body.appendChild(script);
  });
}

jsonp({
  url: 'http://localhost:4000/',
  param: {from: '3000'},
  callback: 'foo',
}).then(res => {
  console.log(res, '获取到结果。。。');
})

/*service*/
let express = require('express');
let app = express();
app.get('/', function(req, res) {
  console.log(req.query, 77);
  res.send(`${req.query.callback}({
    "code": 0,
    "name": "wml",
    "age": 26
  })`);
});
app.listen(4000, function() {
  console.log('server is running...');
});
```

![cross](/img/cross/5.png)

由于`<script>`元素请求的脚本直接作为代码运行，所以浏览器只要定义了foo函数就会立即调用，作为参数的json数据被视为js对象，因此不必使用JSON.parse转换数据。

jsonp接口与普通接口返回数据有区别，所以需要做jsonp数据兼容，可以通过对应的callback关键字判断，有则是jsonp请求，返回jsonp数据，否则返回普通接口数据。

缺陷：基于jsonp的原理，所以jsonp只能是`GET`请求。

### CORS

CORS需要浏览器和后端同时支持，浏览器会自动进行CORS通信，服务端设置`Access-Control-Allow-Origin`可以开启CORS，表示哪些域名可以访问资源。CORS支持所有类型的http请求；虽然CORS和前端没什么关系，但是这种跨域方式会在发送请求时分为两种情况：

1、简单请求

同时满足以下两个条件：

> a、请求方法为GET、HEAD、POST之一  
> b、Content-type的值为text/plain、multipart/form-data、application/x-www-form-urlencoded之一

```js
// client.js
let xhr = new XMLHttpRequest();
xhr.open('GET', 'http://localhost:4000/', true);
xhr.onreadystatechange = function() {
  console.log(xhr.readyState, xhr.status, xhr.response);
}
xhr.send();

// server.js
let express = require('express');
let app = express();
const whiteList = ['http://localhost:7009']
app.get('/', function(req, res) {
  if(whiteList.includes(req.headers.origin)) {
    res.setHeader('Access-Control-Allow-Origin', req.headers.origin);
    res.send({
      "code": 0,
      "name": "wml",
      "age": 18
    });
  }
});
app.listen(4000, function() {
  console.log('server is running...');
});
```

2、复杂请求

不符合简单请求的就是复杂请求，复杂请求会在正式通信之前进行一次预检，即增加一次http查询请求，该请求是用option方法来通知服务端是否允许跨域请求。

```js
//client.js
let xhr = new XMLHttpRequest();
xhr.open('PUT', 'http://localhost:4000/', true);
xhr.setRequestHeader('name', 'wml');
xhr.onreadystatechange = function() {
  console.log(xhr.readyState, xhr.status, xhr.response);
}
xhr.send();

let express = require('express')
let app = express()
let whitList = ['http://localhost:7009'] //设置白名单
app.use(function(req, res, next) {
  let origin = req.headers.origin
  if (whitList.includes(origin)) {
    // 设置哪个源可以访问我
    res.setHeader('Access-Control-Allow-Origin', origin)
    // 允许携带哪个头访问我
    res.setHeader('Access-Control-Allow-Headers', 'name')
    // 允许哪个方法访问我
    res.setHeader('Access-Control-Allow-Methods', 'PUT')
    // 允许携带cookie
    res.setHeader('Access-Control-Allow-Credentials', true)
    // 预检的存活时间
    res.setHeader('Access-Control-Max-Age', 6)
    // 允许返回的头
    res.setHeader('Access-Control-Expose-Headers', 'name')
    if (req.method === 'OPTIONS') {
      res.end() // OPTIONS请求不做任何处理
    }
  }
  next()
})
app.put('/', function(req, res) {
  console.log(req.headers, 88)
  res.setHeader('name', 'wml2') //返回一个响应头，后台需设置
  res.end(`{
    "code": 0,
    "name": "wml",
    "age": 18
  }`)
});
app.listen(4000, function() {
  console.log('server is running...');
});
```

### webSocket

webSocket是H5一个持久化的协议，实现浏览器和服务端的全双工通信，也是一种跨域方案，Websocket和Http协议一样是应用层协议，基于TCP协议，在建立连接后可以实现server和client都能主动发起和接收数据。

```js
// client
let socket = new WebSocket('ws://localhost:4000');
socket.onopen = function() {
  socket.send('I am client');
}
socket.onmessage = function(msg) {
  console.log('receive msg: ', msg.data);
}

// server
let websocket = require('ws');
const webSocketServer = websocket.Server;
const wss = new webSocketServer({
  port: 4000
});
wss.on('connection', function(ws) {
  console.log(`[SERVER] connection()`);
  ws.on('message', function(message) {
    console.log(`server receive msg: ${message}`);
    ws.send(`I am server`, err => {
      console.log(err, 'err');
    })
  })
})
```

### node中间件代理（两次跨域）

同源策略是浏览器要遵循的标准，如果是服务器之间通信则无需遵守，代理服务器的步骤：

接收客户端请求（cors跨域）-> 将请求转发给服务器->拿到服务器响应数据->将响应转发给客户端

```js
// client
let xhr = new XMLHttpRequest();
xhr.open('GET', 'http://localhost:3000/', true);
xhr.send();

// proxy server
const http = require('http');
const server = http.createServer((request, response) => {
  // 浏览器和代理服务器交互设置cors首部字段
  response.writeHead(200, {
    'Access-Control-Allow-Origin': '*',
    'Access-Control-Allow-Methods': '*',
    'Access-Control-Allow-Headers': 'Content-Type'
  });
  // 将请求转发给服务器（4000）
  http.request({
    host: 'localhost',
    port: 4000,
    method: request.method,
    headers: request.headers
  }, res => {
    // 收到响应
    let datas = '';
    res.on('data', chunk => {
      datas += chunk;
    });
    res.on('end', () => {
      console.log(`the data is ${datas}`);
      // 将响应转发给浏览器
      response.end(datas);
    });
  })
  .end();
});
server.listen(3000, function() {
  console.log('the proxy server is running...');
})

// server
const http = require('http');
const data = {
  name: 'wml',
  age: 18
}
const server = http.createServer((request, response) => {
  if(request.url === '/') {
    response.end(JSON.stringify(data));
  }
});
server.listen(4000, () => {
  console.log('the server is running...');
})
```

### nginx反向代理

原理和node中间件代理类似，需搭建一个中转nginx的服务器转发请求，利用nginx配置一个代理服务器（与client端域名相同，端口不同），反向代理server的接口；需要下载nginx

```
server {
  listen 3000,
  server_name (client domain),
  location / {
    proxy_pass (server domain) # 反向代理
  }
}
```

### 相关源码：[github](https://github.com/wumeilian/crossDomain/tree/master/postMessage)


