> [一文彻底学会使用web worker](https://juejin.cn/post/7139718200177983524)

应用场景：处理一些比较耗时的任务，比如图像处理、视频解码等。这样子不会阻塞js主线程的执行。

面试题：假设一个非常耗时复杂的逻辑，代码逻辑已经优化到最优了的前提下要你优化，应该怎么做？

看之前的node gateway使用了web worker来做metric相关的处理。



使用：

```js
// main.js（主线程）

const myWorker = new Worker('/worker.js'); // 创建worker

myWorker.addEventListener('message', e => { // 接收消息
    console.log(e.data); // Greeting from Worker.js，worker线程发送的消息
});

// 这种写法也可以
// myWorker.onmessage = e => { // 接收消息
//    console.log(e.data);
// };

myWorker.postMessage('Greeting from Main.js'); // 向 worker 线程发送消息，对应 worker 线程中的 e.data
```

