**面试题：实现一下 cache request（请求过的数据不再请求）**

使用map进行存储已经请求过的数据，并且处理并发请求。支持缓存实效性。

```js
class CacheableRequest {
  constructor(cacheTime = 5 * 60 * 1000) {
    this.cache = new Map(); // 存储结构：{ url: { data, timestamp, promise } }
    this.cacheTime = cacheTime; // 默认缓存5分钟
  }

  // 核心请求方法
  async request(url) {
    // 1. 检查有效缓存
    if (this.cache.has(url) && this.isCacheValid(url)) {
      console.log('[缓存命中]', url);
      return this.cache.get(url).data;
    }

    // 2. 处理并发请求：避免重复发送相同请求
    const cacheEntry = this.cache.get(url);
    if (!cacheEntry?.promise) {
      const promise = fetch(url)
        .then(response => {
          if (!response.ok) throw new Error(`HTTP ${response.status}`);
          return response.json();
        })
        .then(data => {
          this.cache.set(url, { 
            data, 
            timestamp: Date.now(),
            promise: null 
          });
          return data;
        })
        .catch(error => {
          this.cache.delete(url); // 失败时清除缓存
          throw error;
        });

      this.cache.set(url, { promise }); // 
    }

    return this.cache.get(url).promise;
  }

  // 检查缓存有效性
  isCacheValid(url) {
    const entry = this.cache.get(url);
    return Date.now() - entry.timestamp < this.cacheTime;
  }

  // 手动清除缓存
  clearCache(url) {
    if (url) this.cache.delete(url);
    else this.cache.clear();
  }
}
```

