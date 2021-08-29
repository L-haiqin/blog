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

同源策略限制内容有：

- Cookie、LocalStorage、IndexedDB 等存储性内容
- DOM 节点
- AJAX 请求发送后，结果被浏览器拦截了

但是有三个标签是**允许跨域加载资源**：

- `<img src=xxx>`
- `<link href=xxx>`
- `<script src=xxx>`

##### 解决方案

###### 1、jsonp

利用 `script` 标签没有跨域限制的漏洞，网页可以得到从其他来源动态产生的 JSON 数据。JSONP请求一定需要对方的服务器做支持才可以。

###### 2、cors

**CORS 需要浏览器和后端同时支持。IE 8 和 9 需要通过 XDomainRequest 来实现**。

浏览器会自动进行 CORS 通信，实现 CORS 通信的关键是后端。只要后端实现了 CORS，就实现了跨域。

服务端设置` Access-Control-Allow-Origin `就可以开启 CORS。 该属性表示哪些域名可以访问资源，如果设置通配符则表示所有网站都可以访问资源。

虽然设置 CORS 和前端没什么关系，但是通过这种方式解决跨域问题的话，会在发送请求时出现两种情况，分别为**简单请求**和**复杂请求**。

###### 3、nginx反向代理

###### 4、node中间件代理（两次跨域）

###### 5、websocket

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