### 1 题目一

有一个搜索框，如果用户输入a，会返回匹配的列表数据；紧接着用户输入ab，希望列表继续展示a的搜索结果，等待ab搜索结果返回后在展示，这个怎么实现？

可以使用useDeferredValue，可以延迟ui的渲染，对过时的内容可以让字体颜色变浅。

https://zh-hans.react.dev/reference/react/useDeferredValue#indicating-that-the-content-is-stale

```js
import { Suspense, useState, useDeferredValue } from 'react';
import SearchResults from './SearchResults.js';

export default function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query); // 关键点
  const isStale = query !== deferredQuery;
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <div style={{
          opacity: isStale ? 0.5 : 1, // 关键点
          transition: isStale ? 'opacity 0.2s 0.2s linear' : 'opacity 0s 0s linear'
        }}>
          <SearchResults query={deferredQuery} /> // 搜索结果展示，列表展示
        </div>
      </Suspense>
    </>
  );
}

```

### 2 题目二：竞态问题

有一个场景，发送多次请求，怎么保证参数与请求对应？

当用户快速输入时，可能会发送多个搜索请求。这些请求可能不会按照发送顺序返回，因为网络延迟等原因。因此，如果不进行处理，可能会出现以下情况：

用户输入了“a”，发送了请求1。

用户输入了“ab”，发送了请求2。

请求1返回了结果，但此时用户已经输入了“abc”，请求2还没返回。

请求2返回了结果，覆盖了请求1的结果，但实际上请求2对应的输入已经过时了。

**竞态问题**

**方法一：取消请求**

每次请求服务器的时候，就取消前面已经发出的请求。

**AbortController（Fetch API）**

```js
let controller = null;

async function search(keyword) {
  if (controller) controller.abort(); // 取消上次请求
  controller = new AbortController();
  try {
    const res = await fetch(`/api/search?q=${keyword}`, {
      signal: controller.signal
    });
    // 处理响应
  } catch (err) {
    if (err.name !== 'AbortError') throw err; // 忽略取消导致的错误
  }
}
```

**Axios CancelToken**

axios自带的CancelToken方法。

```js
const CancelToken = axios.CancelToken;
let cancel;

function search(keyword) {
  if (cancel) cancel('请求已取消');
  return axios.get(`/api/search?q=${keyword}`, {
    cancelToken: new CancelToken(c => (cancel = c))
  }).then(res => {
    // 更新结果
  }).catch(err => {
    if (!axios.isCancel(err)) console.error(err);
  });
}
```

**方法二：请求标记策略（忽略过期响应）**

通过唯一标识标记每次请求，仅处理最新请求的响应。

比如时间戳，唯一id等。

```js
let latestReqId = 0; // 可以使用ref

async function search(keyword) {
  const reqId = ++latestReqId; // 生成唯一标识
  const res = await fetch(`/api/search?q=${keyword}`);
  const data = await res.json();
  if (reqId !== latestReqId) return; // 非最新请求则忽略
  updateUI(data); // 更新界面
}
```

**方法三：队列管理（保证顺序一致性）**

跟上面的请求标识差不多。

维护请求队列，仅处理最后一次请求的结果：

```js
let queue = [];
async function search(keyword) {
  const reqId = Date.now();
  queue.push(reqId);
  const res = await fetch(`/api/search?q=${keyword}`);
  if (queue[queue.length - 1] !== reqId) return; // 非最新请求则忽略
  queue = []; // 清空队列
  updateUI(res);
}
```

**方法四：RxJS 响应式编程（自动取消旧请求）**

使用 `switchMap` 操作符自动取消未完成的旧请求，自动管理请求生命周期：

```js
import { fromEvent } from 'rxjs';
import { debounceTime, switchMap } from 'rxjs/operators';

const input = document.querySelector('input');
fromEvent(input, 'input')
  .pipe(
    debounceTime(300), // 防抖
    switchMap(e => fetch(`/api/search?q=${e.target.value}`))
  )
  .subscribe(res => updateUI(res));
```

还有一些其他第三方库的使用，比如：useSWR，useRequest，都可以进行 缓存管理 和 自动去重 处理

[从 SWR 开始 — 一窥现代请求 hooks 设计模型](https://developer.aliyun.com/article/786572)

**方法五：防抖节流**

### 3 题目三：cache request

实现一下 cache request（请求过的数据不再请求）

使用map进行存储已经请求过的数据，并且处理并发请求。支持缓存实效性。

```js
type Cache = {
  timestamp: number; // 请求返回后的时间戳，用于判断是否过期
  data: any; // response
  promise: Promise<any>; // fetch请求，用于处理并发
};

class CacheRequest {
  private cache; // {url:{timestamp,data,promise}}
  private cacheTime;
  constructor() {
    this.cache = new Map();
    this.cacheTime = 5 * 60 * 1000; // 默认过期时间为5min
  }

  request(url) {
    // 缓存存在，且没有过期，则使用缓存的数据
    if (this.cache.has(url) && this.isValidateTime(url)) {
      return this.cache.get(url).data;
    }

    // 处理并发请求，如果当前请求已经在处理中，则不需要再次请求
    const curCache = this.cache.get(url);
    if (!curCache?.promise) {
      const promise = fetch(url)
        .then(res => {
          this.cache.set(url, {
            timestamp: Date.now(),
            data: res?.data,
            promise: null,
          });
          return res?.data;
        })
        .catch(e => {
          console.log(e);
          // 失败时清除缓存
          this.cache.delete(url);
        });

      this.cache.set(url, { promise });
    }

    // 当前请求在处理中，直接返回response
    return this.cache.get(url).promise;
  }

  // 缓存是否过期
  isValidateTime(url) {
    const { timestamp } = this.cache.get(url);
    return Date.now() - timestamp < this.cacheTime;
  }
}
```

