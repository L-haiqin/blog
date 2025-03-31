> [Window：requestAnimationFrame() 方法](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)

### 1 定义

**`window.requestAnimationFrame()`** 方法会告诉浏览器你希望执行一个动画。它要求浏览器在下一次**重绘之前**，调用用户提供的回调函数。

> 若你想在浏览器下次重绘之前继续更新下一帧动画，那么回调函数自身必须再次调用 `requestAnimationFrame()`。`requestAnimationFrame()` 是一次性的。

![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c7885e81d59849868445d3c0b37d655d~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)



`requestAnimationFrame(callback)`

- callback
  - 该函数会在下一次重绘更新你的动画时被调用到。这个回调函数只会传递一个参数：一个 [`DOMHighResTimeStamp`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMHighResTimeStamp) 参数，用于表示上一帧渲染的结束时间（基于 [time origin](https://developer.mozilla.org/zh-CN/docs/Web/API/Performance/timeOrigin) 的毫秒数）
  - 时间戳是一个以毫秒为单位的十进制数字，最小精度为 1 毫秒。对于 `Window` 对象（而非 `workers`）来说，它等同于 [`document.timeline.currentTime`](https://developer.mozilla.org/en-US/docs/Web/API/AnimationTimeline/currentTime)。此时间戳在同一代理上（所有同源的 `window`，更重要的是同源的 `iframe`）运行的所有窗口之间共享——它允许在多个 `requestAnimationFrame` 回调函数中执行同步动画。此时间戳值也近似于在回调函数开始时调用 [`performance.now()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Performance/now)，但它们永远都不会是相同的值。
  - 当 `requestAnimationFrame()` 队列中的多个回调开始在同一帧中触发时，它们都会收到相同的时间戳，即便在计算前一个回调函数工作量时这一帧的时间已经过去。

- Response

  请求 ID 是一个 `long` 类型整数值，是在回调列表里的唯一标识符。这是一个非零值，但你不能对该值做任何其他假设。你可以将此值传递给 [`window.cancelAnimationFrame()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/cancelAnimationFrame) 函数以取消该刷新回调请求。

### 2 使用

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>requestAnimationFrame 示例</title>
</head>
<body>

<script>
  let startTime;

  function animate(timestamp) {
    if (!startTime) {
      startTime = timestamp;
    }

    const progress = timestamp - startTime;

    // 执行动画操作，例如移动元素
    const element = document.getElementById('animatedElement');
    element.style.transform = `translateX(${progress * 0.1}px)`;

    if (progress < 2000) { // 设置动画执行的总时间
      requestAnimationFrame(animate);
    }
  }

  // 启动动画
  requestAnimationFrame(animate);
</script>

<style>
  #animatedElement {
    width: 50px;
    height: 50px;
    background-color: blue;
    position: absolute;
  }
</style>

<div id="animatedElement"></div>

</body>
</html>
```

这个示例演示了如何使用 `requestAnimationFrame` 创建一个简单的水平移动动画。`requestAnimationFrame` 的优势之一是它在浏览器的重绘之前执行，因此通常比 `setTimeout` 和 `setInterval` 更为高效。

### 3 与setTimeout、setInternal区别

`requestAnimationFrame`：

- 用于动画效果的开发，以优化动画的性能。
- 在浏览器准备好绘制下一帧屏幕时调用传入的回调函数。
- 不会在页面隐藏或最小化时运行，从而减少资源浪费。
- 调用频率与屏幕刷新率同步，可以提供平滑的动画效果。

`setTimeout`：

- 延迟一定时间后调用传入的回调函数。
- 仅调用一次。
- 时间精度不太准确，会受到浏览器当前忙碌程度的影响。
- 用于创建简单的计时器、轮询和非重要操作。

`setInterval`：

- 在一定时间间隔后调用传入的回调函数，直到被清除。
- 可以用于创建重复执行的计时器、轮询和非重要操作。
- 时间精度不太准确，同样会受到浏览器当前忙碌程度的影响。

`setImmediate`：

- 在当前事件循环迭代结束后立即执行传入的回调函数。
- 相当于将回调函数插入到事件队列的头部。
- 用于在一些异步操作完成后立即执行回调函数。

