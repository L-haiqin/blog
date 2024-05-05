### js获取url参数，并返回参数对象

url类似长这样的： https://www.nowcoder.com/discuss/773561?type=post&order=create&pos=&page=1&ncTraceId=&channel=-1&source_id=search_post_nctrack&subType=2 

#### 方法一：split()

`split()`根据`?`和`&`两次分割。

```javascript
//路径 https://editor.csdn.net/md?articleId=1&&articleNum=1&opt=edit
function getUrlParams(url){
    //let url = window.location.search;//?articleId=1&&articleNum=1&opt=edit
    let index = url.indexOf('?');
    let obj = {};
    if (index !== -1) {
      // let str = url.substr(1);
      let str = url.split('?')[1];
      let arr = str.split('&');
      for (let i = 0; i < arr.length; i++) {
        let u = arr[i].split('=');
        obj[u[0]] = u[1];
      }
    }
    return obj; //{articleId: "1", articleNum: "1", opt: "edit"}
  }

console.log(getUrlParams('https://editor.csdn.net/md?articleId=1&&articleNum=1&opt=edit'))
```

`window.location.search`可以获取url参数。

#### 方法二：正则

```javascript
//路径 https://editor.csdn.net/md?articleId=1&&articleNum=1&opt=edit
function getUrlParams(locationSearch){
    var obj = {};
    var search = locationSearch;
    search = /\?/.test(search) && search.split("?")[1];// \? 表示一个 ? 字符
    var searchs = /\&/.test(search) ? search.split("&") : [search];
    for (var i = 0; i < searchs.length; i++) {
        if (/\=/.test(searchs[i])) {
            var item = searchs[i].split("=");
            obj[item[0]] = item[1];
        };
    };
    return obj;
  }

console.log(getUrlParams('?articleId=1&&articleNum=1&opt=edit')) // {articleId: "1", articleNum: "1", opt: "edit"}
```

#### 方法三：使用URL对象

[URL API MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/URL)

```js
// https://some.site/?id=123
const parsedUrl = new URL(window.location.href);
console.log(parsedUrl.searchParams.get("id")); // "123"
```

**反向例子：**

给定一个baseurl=https://some.site，和参数对象params={'a':1,'b':2}，实现拼接后的url=https://some.site?a=1&b=2，需要考虑null、undefined的情况。类似于[axios get请求](https://jsnoteclub.com/axios/getting-started/axios-get/)。

```js
const axios = require("axios")

axios
  .get("/user", {
    params: {
      ID: 12345 // here，会被拼接成 ?ID=12345
    }
  })
  .then(function (response) {
    console.log(response)
  })
  .catch(function (error) {
    console.log(error)
  })
  .finally(function () {
    // 总是会执行
  })
```

方法一：手动遍历params对象，处理异常情况。

方法二：使用[URLSearchParams](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams)。提供append、delete、set等方法，可以修改search params的值。

```js
var params4 = new URLSearchParams({ foo: 1, bar: 2 });
```

