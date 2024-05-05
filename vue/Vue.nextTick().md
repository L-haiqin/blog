### Vue.nextTick()

结构：[Vue.nextTick( [callback, context] )](https://cn.vuejs.org/v2/api/#Vue-nextTick)

用法：

在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

应用场景：

一般用于 **`this.key = newVal` 更改数据后，想立即获取更改过后的 DOM 数据。**

```javascript
// 修改数据
vm.msg = 'Hello'
// DOM 还没有更新
Vue.nextTick(function () {
  // DOM 更新了
})

// 作为一个 Promise 使用，因为 $nextTick() 返回了一个 Promise 对象
Vue.nextTick()
  .then(function () {
    // DOM 更新了
  })
```

原理：

Vue.nextTick 或者 vm.$nextTick 的原理其实很简单，就做了两件事：

- 将传递的回调函数用 `try catch` 包裹然后放入 callbacks 数组
- 执行 timerFunc 函数，在浏览器的异步任务队列放入一个 flushCallbacks 数组的函数（首选promise.then，再setImmediate，最后setTimeout）

```javascript
// /src/core/util/next-tick.js

const callbacks = []
let pending = false

/**
 * 完成两件事：
 *   1、用 try catch 包装 flushSchedulerQueue 函数，然后将其放入 callbacks 数组
 *   2、如果 pending 为 false，表示现在浏览器的任务队列中没有 flushCallbacks 函数
 *     如果 pending 为 true，则表示浏览器的任务队列中已经被放入了 flushCallbacks 函数，
 *     待执行 flushCallbacks 函数时，pending 会被再次置为 false，表示下一个 flushCallbacks 函数可以进入
 *     浏览器的任务队列了
 * pending 的作用：保证在同一时刻，浏览器的任务队列中只有一个 flushCallbacks 函数
 * @param {*} cb 接收一个回调函数 => flushSchedulerQueue
 * @param {*} ctx 上下文
 * @returns 
 */
export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  // 用 callbacks 数组存储经过包装的 cb 函数
  callbacks.push(() => {
    if (cb) {
      // 用 try catch 包装回调函数，便于错误捕获
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  if (!pending) {
    pending = true
    // 执行 timerFunc，在浏览器的任务队列中（首选微任务队列）放入 flushCallbacks 函数
    timerFunc()
  }
  // $flow-disable-line
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
```