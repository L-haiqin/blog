### 手写jsonp

JSONP由两部分组成：**回调函数和数据**。回调函数是当响应到来时应该在页面中调用的函数。回调函数的名字一般是在请求中指定的。而数据就是传入回调函数中的JSON数据。

```javascript
callback({ “name”: “Nicholas” });
```

下面是一个典型的JSONP请求。

```awk
http://freegeoip.net/json/?callback=handleResponse
```

这里指定的回调函数的名字叫handleResponse() 。

代码如下：

```javascript
function jsonp ({url, query}) {
  // 1.创建script标签
  let script = document.createElement("script");
  // 2.拼接url
  let cb = `jsonpCallBack${new Date().getTime()}${Math.floor(Math.random(5)*100000)}`
  let params = {...query, cb}

  let arr = []
  for (let key in params) {
    arr.push(`${key}=${params[key]}`)
  }

  // 3.设置script.src，并立即请求
  script.src = `${url}?${arr.join("&")}`
  document.body.appendChild(script)

  // 4.声明一个唯一的回调函数并挂载到全局上
  // 触发callback，触发后删除js标签和绑定在window上的callback
  return new Promise((resolve, reject) => {
    window[cb] = function (res) {
      if (res) resolve(res);
      else reject('没有返回数据');
      document.body.removeChild(script);
      delete window[cb];
    }
  })
}

jsonp({
  url:'/getList',
  query: {name: 'ys',age: 19}
}).then((res) => {
  console.log(res)
}).catch((e) => {
  console.log(e);
})

// 服务器端:
// 1. 获取参数, 拿到回调函数名称
// 2. 并返回前端 该回调的调用, 把返回结果当作参数传入
```

JSONP原理：**动态生成一个JavaScript标签，其src由接口url、请求参数、callback函数名拼接而成，利用js标签没有跨域限制的特性实现跨域请求。** 

注意：

- callback函数要绑定在window对象上；
- 服务端返回数据有特定格式要求：`callback函数名+'('+JSON.stringify(返回数据) +')'`；

- 不支持post，因为js标签本身就是一个get请求。

一个简单的jsonp请求：

```javascript
function handleResponse(response){
    alert(“You’re at IP address ” + response.ip + ”, which is in ” + response.city + ”, ” + response.region_name);
}

var script = document.createElement(“script”);
script.src = “http://freegeoip.net/json/?callback=handleResponse”;
document.body.insertBefore(script, document.body.firstChild);
```

这个例子通过查询地理定位服务来显示你的IP地址和位置信息。