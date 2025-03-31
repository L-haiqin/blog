> [2022年了,还不懂requestIdleCallback么？](https://juejin.cn/post/7061947637167194142)、[requestIdleCallback](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestIdleCallback)

### 1 定义

**`window.requestIdleCallback()`** 方法插入一个函数，这个函数将在浏览器空闲时期被调用。

`requestIdleCallback(callback, options)`

- [`callback`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestIdleCallback#callback)

  一个在事件循环空闲时即将被调用的函数的引用。函数会接收到一个名为 [`IdleDeadline`](https://developer.mozilla.org/zh-CN/docs/Web/API/IdleDeadline) 的参数，这个参数可以获取当前空闲时间以及回调是否在超时时间前已经执行的状态。

- [`options` 可选](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestIdleCallback#options)

  包括可选的配置参数。具有如下属性：`timeout`：如果指定了 timeout，并且有一个正值，而回调在 timeout 毫秒过后还没有被调用，那么回调任务将放入事件循环中排队，即使这样做有可能对性能产生负面影响。

- Response

  一个 ID，可以把它传入 [`Window.cancelIdleCallback()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/cancelIdleCallback) 方法来结束回调。

### 2 使用

```js
type Deadline = {
  timeRemaining: () => number // 当前剩余的可用时间。即该帧剩余时间。
  didTimeout: boolean // 是否超时。
}

function work(deadline:Deadline) { // deadline 上面有一个 timeRemaining() 方法，能够获取当前浏览器的剩余空闲时间，单位 ms；有一个属性 didTimeout，表示是否超时
  console.log(`当前帧剩余时间: ${deadline.timeRemaining()}`);
  if (deadline.timeRemaining() > 1 || deadline.didTimeout) {
     // 走到这里，说明时间有余，我们就可以在这里写自己的代码逻辑
  }
  // 走到这里，说明时间不够了，就让出控制权给主线程，下次空闲时继续调用
  requestIdleCallback(work);
}
requestIdleCallback(work, { timeout: 1000 }); // 这边可以传一个回调函数（必传）和参数（目前就只有超时这一个参数）
```

**面试题：如何获取浏览器剩余时间？**

> 使用requestIdleCallback，第一个参数callback接受一个deadLine的数据，deadLine.timeRamaining表示浏览器剩余的时间。

### 3 应用场景

场景：针对不重要且不紧急的低优先级任务。

**不要在requestIdleCallback里面对dom元素进行操作**。因为在requestIdleCallback之前页面元素已经渲染出来了，如果再对其进行更改的话，可能会导致页面闪烁的情况，造成页面元素的重排重绘。

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6c897de9b44c45faa24fced21b38c3a6~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### 3.1 数据的分析和上报

```js
const queues = [];
const btns = btns.forEach(btn => {
    btn.addEventListener('click', e => {
        // do something
        pushQueue({
          type: 'click'
          // ...
        }));
        schedule(); // 等到空闲再处理
    });
});
function schedule() {
    requestIdleCallback(deadline => {
          while (deadline.timeRemaining() > 1) {
              const data = queues.pop();
              // 空闲的时候，在这里处理数据、上传数据
          }
          if (queues.length) schedule();
    });
}
```

#### 3.2 预加载

在空闲的时候加载些东西，可以看看 [qiankun](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fumijs%2Fqiankun%2Fblob%2Fmaster%2Fsrc%2Fprefetch.ts) 的例子，用来预加载 js 和 css。

```js
function prefetch(entry: Entry, opts?: ImportEntryOpts): void {
  if (!navigator.onLine || isSlowNetwork) {
    // Don't prefetch if in a slow network or offline
    return;
  }

  requestIdleCallback(async () => {
    const { getExternalScripts, getExternalStyleSheets } = await importEntry(entry, opts);
    requestIdleCallback(getExternalStyleSheets);
    requestIdleCallback(getExternalScripts);
  });
}
```

#### 3.3 检测卡顿

> [什么是FPS(Frames per second)？](https://zhuanlan.zhihu.com/p/150243108)
>
> FPS: 每秒帧数

一般检测的卡顿方法有两种：

- 测量 fps 值，如果连续出现几个 fps 值 ≤ 阈值，则认为是卡顿。
  - 通常 FPS 在 60 的时候是比较流畅的, 而 FPS 比较低的时候就会感觉到卡顿。
- 开辟一个 worker 线程和主线程之间来个心跳检测，一段时间内没响应，则认为是卡顿。

比如，如果 ***requestIdleCallback*** 长时间内没能得到执行，说明一直没有空闲时间，很有可能就是发生了卡顿，从而可以打点上报。

它比较适用于行为卡顿，举个例子：点击某个按钮并同时添加我们的 ***requestIdleCallback*** 回调，如果点击后的一段时间内这个回调没有得到执行，很大概率是这个点击操作造成了卡顿。

#### 3.4 拆分耗时任务

 [React 中的调度器 Scheduler](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ffacebook%2Freact%2Fblob%2F1fb18e22ae66fdb1dc127347e169e73948778e5a%2Fpackages%2Fscheduler%2Fsrc%2FScheduler.js)

### 4 与react的调度算法的关系

### 5 与requestAnimationFrame区别

- requestIdleCallback 是利用帧之间空闲时间来执行JS。

- requestIdleCallback 是在 layout 和 paint 之后, 意味着requestIdleCallback 是可以js计算并改变DOM的，也就是说会 触发重新 layout 和 paint。

- requestAnimationFrame 是在 layout 和 paint 之前，因此更适合变更DOM操作。

- 因此React内部对调度策略的实现也是基于requestAnimationFrame的。

![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c7885e81d59849868445d3c0b37d655d~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

### 6 模拟实现requestIdleCallback

https://juejin.cn/post/7061947637167194142#heading-8

```js
window.requestIdleCallback = function(cb) {
    let start = Date.now();
    return setTimeout(function () {
      const deadline = { // 这边就是为了构造参数
        timeRemaining: () => Math.max(0, 50 - (Date.now() - start)), // 剩余时间我们写死在 50ms 内，也就是前面提到的上限值，其实你也可以写成 40、30、16、10 等😂
        didTimeout: false // 因为我们不推荐使用 timeout 参数，所以这里就直接写死 false
      };
      cb(deadline);
    });
}
```

