面试题1：**请求跨域了，那么请求到底发出去没有？**

> **跨域并不是请求发不出去，请求能发出去，服务端能收到请求并正常返回结果，只是结果被浏览器拦截了**。
>
> 你可能会疑问明明通过表单的方式可以发起跨域请求，为什么 Ajax 就不会?
>
> 因为归根结底，跨域是为了阻止用户读取到另一个域名下的内容，Ajax 可以获取响应，浏览器认为这不安全，所以拦截了响应。但是**表单并不会获取新的内容，所以可以发起跨域请求**。同时也说明了跨域并不能完全阻止 CSRF，因为请求毕竟是发出去了。



### ajax

#### 概念

Asynchronous JavaScript and XML（异步JavaScript和XML） 

xml是一种数据格式，但目前大部分是使用json这种格式来代替xml，因为前者更加简洁，解析速度也要快一些。

用处：

- 网页应用能够快速地将**增量**更新呈现在用户界面上，而不需要重载（刷新）整个页面。 

#### 核心（XMLHttpRequest）

[`XMLHttpRequest API`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest#handling_responses) 是Ajax的核心。 

`XMLHttpRequest`（XHR）对象用于与服务器交互。通过 XMLHttpRequest 可以在不刷新页面的情况下请求特定 URL，获取数据。这允许网页在不影响用户操作的情况下，更新页面的局部内容。`XMLHttpRequest` 在 [AJAX](https://developer.mozilla.org/zh-CN/docs/Glossary/AJAX) 编程中被大量使用。 

##### 构造函数：

- `XMLHttpRequest()`  

1、标准浏览器通过 `XMLHttpRequest` 对象实现了ajax的功能， 只需要通过一行语句便可创建一个用于发送ajax请求的对象。

```javascript
var xhr = new XMLHttpRequest();复制代码
```

2、IE浏览器通过 `XMLHttpRequest` 或者 `ActiveXObject` 对象同样实现了ajax的功能。

全平台兼容的XMLHttpRequest对象：

```javascript
function getXHR(){
  var xhr = null;
  if(window.XMLHttpRequest) {
    xhr = new XMLHttpRequest();
  } else if (window.ActiveXObject) {
    try {
      xhr = new ActiveXObject("Msxml2.XMLHTTP");
    } catch (e) {
      try {
        xhr = new ActiveXObject("Microsoft.XMLHTTP");
      } catch (e) { 
        alert("您的浏览器暂不支持Ajax!");
      }
    }
  }
  return xhr;
}
```

##### 属性：

- `readyState`
- `onreadystatechange`
- `response`
  - 返回的类型为 [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) 、 [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob) 、 [`Document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document) 、 JavaScript [`Object`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object) 或 [`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString) 中的一个。 这取决于 [`responseType`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/responseType) 属性。 
- `responseText`
- `responseType`
- `responseURL`
- `responseXML`
- `status`
- `statusText`
-  `timeout`
  - 当超时发生， [timeout](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/timeout_event) 事件将会被触发。 

- `upload`
  - 返回一个 `XMLHttpRequestUpload`对象，用来表示上传的进度。这个对象是不透明的，但是作为一个[`XMLHttpRequestEventTarget`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequestEventTarget)，可以通过对其绑定事件来追踪它的进度。 
- `withCredentials`

##### 方法：

- `open()`
  - ` xhrReq.open(method, url, async, user, password);`
  - 初始化一个请求 
- `send()`
  - 发送 HTTP 请求
  - 如果是异步请求（默认为异步请求），则此方法会在请求发送后立即返回；如果是同步请求，则此方法直到响应到达后才会返回。
  - `XMLHttpRequest.send() `方法接受一个可选的参数，其作为请求主体；如果请求方法是 GET 或者 HEAD，则应将请求主体设置为 null。 
- `abort()`
  - 终止请求
- `getAllResponseHeaders()`
- `getResponseHeader()`
- `setRequestHeader()`
- `overrideMimeType()`
  - 指定一个MIME类型用于替代服务器指定的类型，使服务端响应信息中传输的数据按照该指定MIME类型处理。 
  - `mimeType`指定具体的MIME类型去代替有服务器指定的MIME类型.，如果服务器没有指定类型，那么 `XMLHttpRequest` 将会默认为 `"text/xml"`。

##### 事件：

- `onreadystatechange`
  - `XMLHttpRequest.onreadystatechange = callback;`
  - 当 `readyState` 的值改变的时候，`callback` 函数会被调用。
- `abort`
  -  当一个请求终止时 `abort` 事件被触发，比如程序执行 [`XMLHttpRequest.abort()`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/abort)。 
- `error`  
- `load`
- `loadstart`
  -  当程序开始加载时，loadstart 事件将被触发。
- `loadend`
  -  loadend事件总是在一个资源的加载进度停止之后被触发 (例如，在已经触发“error”，“abort”或“load”事件之后)。 
-  `progress`
  - `progress`事件会在请求接收到数据的时候被周期性触发。 

- `timeout`

##### 继承：

-  `XMLHttpRequestEventTarget `

-  `EventTarget `
  -  [`Element`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element)，[`document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document) 和 [`window`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window) 是最常见的 event targets ，但是其他对象也可以作为 event targets，比如 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)，[`AudioNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioNode)，[`AudioContext`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext) 等等。 

- 继承关系图：
  - `XMLHttpRequest`   --->   `XMLHttpRequestEventTarget `   --->    `EventTarget `

参考博客： [XMLHttpRequest MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest#handling_responses)

#### 常使用的ajax技术

原生ajax、jquery封装后的 `$.ajax()  `、`axios`、`fetch`

axios源码：https://github.com/axios/axios

#### 其他

ajax文件上传、ajax请求二进制文件

#### ajax跨域请求

##### 同源策略

协议、域名（子域名、主域名）、端口号

**同源策略限制内容**有：

- Cookie、LocalStorage、IndexedDB 等存储性内容
- DOM 节点
- AJAX 请求发送后，结果被浏览器拦截了

但是有三个标签是**允许跨域加载资源**：

- `<img src=xxx>`
- `<link href=xxx>`
- `<script src=xxx>`

##### 解决方案

###### 1、jsonp

**1）JSONP原理**

利用 `<script>` 标签没有跨域限制的漏洞，网页可以得到从其他来源**动态**产生的 JSON 数据。

JSONP请求一定需要对方的服务器做支持才可以。

**2） JSONP和AJAX对比**

JSONP和AJAX相同，都是客户端向服务器端发送请求，从服务器端获取数据的方式。但**AJAX属于同源策略，JSONP属于非同源策略（跨域请求）**。

**3）JSONP优缺点**

JSONP优点是简单兼容性好，可用于解决主流浏览器的跨域数据访问的问题。**缺点是仅支持get方法具有局限性,不安全可能会遭受XSS攻击。**

###### 2、cors

**CORS 需要浏览器和后端同时支持。IE 8 和 9 需要通过 XDomainRequest 来实现**。

浏览器会自动进行 CORS 通信，实现 CORS 通信的关键是后端。只要后端实现了 CORS，就实现了跨域。

服务端设置` Access-Control-Allow-Origin `就可以开启 CORS。 该属性表示哪些域名可以访问资源，如果设置通配符则表示所有网站都可以访问资源。

虽然设置 CORS 和前端没什么关系，但是通过这种方式解决跨域问题的话，会在发送请求时出现两种情况，分别为**简单请求**和**复杂请求**。

###### 3、nginx反向代理

###### 4、node中间件代理（两次跨域）

###### 5、websocket

Websocket是HTML5的一个持久化的协议，它实现了浏览器与服务器的**全双工通信**，同时也是跨域的一种解决方案。

WebSocket和HTTP都是应用层协议，都基于 TCP 协议。但是 **WebSocket 是一种双向通信协议，在建立连接之后，WebSocket 的 server 与 client 都能主动向对方发送或接收数据**。同时，WebSocket 在建立连接时需要借助 HTTP 协议，连接建立好了之后 client 与 server 之间的双向通信就与 HTTP 无关了。

特点包括：

（1）建立在 TCP 协议之上，服务器端的实现比较容易。

（2）与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

（3）数据格式比较轻量，性能开销小，通信高效。

（4）可以发送文本，也可以发送二进制数据。

（5）**没有同源限制**，客户端可以与任意服务器通信。

（6）协议标识符是`ws`（如果加密，则为`wss`），服务器网址就是 URL。

> ```markup
> ws://example.com:80/some/path
> ```

客户端的API：

构造函数：[`WebSocket(url[, protocols\])`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/WebSocket)返回一个 `WebSocket` 对象

属性：

- [`WebSocket.binaryType`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/binaryType)，使用二进制的数据类型连接。
- [`WebSocket.bufferedAmount`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/bufferedAmount) 只读，未发送至服务器的字节数。

- [`WebSocket.extensions`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/extensions) 只读，服务器选择的扩展。

- [`WebSocket.onclose`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/onclose)，用于指定连接关闭后的回调函数。
- [`WebSocket.onerror`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/onerror)，用于指定连接失败后的回调函数。
- [`WebSocket.onmessage`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/onmessage)，用于指定当从服务器接受到信息时的回调函数。
- [`WebSocket.onopen`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/onopen)，用于指定连接成功后的回调函数。
- [`WebSocket.protocol`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/protocol) 只读，服务器选择的下属协议。
- [`WebSocket.readyState`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/readyState) 只读，当前的链接状态。
- [`WebSocket.url`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/url) 只读，WebSocket 的绝对路径。

方法：

- [`WebSocket.close([code[, reason\]])`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/close)，关闭当前链接。
- [`WebSocket.send(data)`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/send)，对要传输的数据进行排队。

事件：使用 `addEventListener()` 或将一个事件监听器赋值给本接口的 `oneventname` 属性，来监听下面的事件。

- [`close` (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket/close_event)，当一个 `WebSocket` 连接被关闭时触发。 也可以通过 [`onclose`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/onclose) 属性来设置。
- [`error`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/error_event)，当一个 `WebSocket` 连接因错误而关闭时触发，例如无法发送数据时。 也可以通过 [`onerror`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/onerror) 属性来设置.
- [`message`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/message_event)，当通过 `WebSocket` 收到数据时触发。 也可以通过 [`onmessage`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/onmessage) 属性来设置。
- [`open` (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket/open_event)，当一个 `WebSocket` 连接成功时触发。 也可以通过 [`onopen`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket/onopen) 属性来设置。

服务端的实现：

常用的 Node 实现有以下三种，

- [µWebSockets](https://github.com/uWebSockets/uWebSockets)
- [Socket.IO](http://socket.io/)
- [WebSocket-Node](https://github.com/theturtle32/WebSocket-Node)

###### 6、postMessage

###### 7、window.name+iframe

###### 8、location.hash+iframe

###### 9、document.domain+iframe

##### iframe标签

`<iframe>`标签是一个内联标签，即在当前html页面中嵌入另一个文档。

###### 基本语法：

  ```html
<iframe src="文件路径"></iframe>  
  ```

###### 常用属性：

- frameborder:是否显示边框，1(yes),0(no)

- height:框架作为一个普通元素的高度，建议在使用css设置。

- width:框架作为一个普通元素的宽度，建议使用css设置。

- name:框架的名称，window.frames[name]时专用的属性。

- scrolling:框架的是否滚动。yes,no,auto。

- src：内框架的地址，可以使页面地址，也可以是图片的地址。

- srcdoc , 用来替代原来HTML body里面的内容。但是IE不支持, 不过也没什么卵用

- sandbox: 对iframe进行一些列限制，IE10+支持

###### 获取iframe里的内容：

- `iframe.contentWindow`： 获取iframe的window对象
- `iframe.contentDocument`：获取iframe的document对象 

###### 在iframe中获取父级内容：

- `window.parent` ：获取上一级的window对象，如果还是iframe则是该iframe的window对象
- `window.top`： 获取最顶级容器的window对象，即，就是你打开页面的文档
- `window.self` ：返回自身window的引用。可以理解 window===window.self(脑残)

参考博客：[Web前端之iframe详解](https://www.cnblogs.com/hq233/p/9849939.html)

##### 总结

- JSONP只支持GET请求，JSONP的优势在于支持老式浏览器，以及可以向不支持CORS的网站请求数据。

- CORS支持所有类型的HTTP请求，是跨域HTTP请求的根本解决方案。

- 不管是Node中间件代理还是nginx反向代理，主要是通过同源策略对服务器不加限制。

- 日常工作中，用得比较多的跨域方案是cors和nginx反向代理。

参考博客：[九种跨域方式实现原理（完整版）](https://juejin.cn/post/6844903767226351623#heading-2)

#### 问题

问：原生js ajax请求有几个步骤？

```javascript
//创建 XMLHttpRequest 对象
var ajax = new XMLHttpRequest();
//规定请求的类型、URL 以及是否异步处理请求。
ajax.open('GET',url,true);
//发送信息至服务器时内容编码类型
ajax.setRequestHeader("Content-type", "application/x-www-form-urlencoded"); 
//发送请求
ajax.send(null);  
//接受服务器响应数据
ajax.onreadystatechange = function () {
    if (obj.readyState == 4 && (obj.status == 200 || obj.status == 304)) { 
    }
};
```

参考博客：[Ajax 知识体系大梳理](https://juejin.cn/post/6844903469896171533#heading-56)、[ajax常见面试题](https://juejin.cn/post/6844903573529034759#heading-0)

ajax源码：https://github.com/littleBlack520/ajax