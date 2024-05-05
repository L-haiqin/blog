### 事件循环

#### 问题

nodejs中的事件循环与浏览器中的事件循环有什么区别？

[浏览器与Node的事件循环(Event Loop)有何区别?](https://zhuanlan.zhihu.com/p/54882306)

#### 前言

Node.js 采用 V8 作为 js 的解析引擎，而 I/O 处理方面使用了自己设计的 libuv。

Node.js 的运行机制:

- **V8 引擎解析 JavaScript 脚本**。
- 解析后的代码，调用 Node API。
- libuv 库负责 Node API 的执行。它将不同的任务分配给不同的线程，形成一个 **Event Loop（事件循环）**，以**异步**的方式将任务的执行结果返回给 V8 引擎。
- V8 引擎再将结果返回给用户。

#### libuv引擎

libuv 引擎中的事件循环分为 6 个阶段，它们会按照顺序反复运行。

每当进入某一个阶段的时候，都会从对应的回调队列中取出函数去执行。当队列为空或者执行的回调函数数量到达系统设定的阈值，就会进入下一阶段。

![img](https://pic4.zhimg.com/80/v2-de1858abd236bdc70904525c3c5b05d7_720w.jpg)

事件循环的顺序：

外部输入数据–>轮询阶段(poll)–>检查阶段(check)–>关闭事件回调阶段(close  callback)–>定时器检测阶段(timer)–>I/O 事件回调阶段(I/O  callbacks)–>闲置阶段(idle, prepare)–>轮询阶段（按照该顺序反复运行）…

- timers 阶段：这个阶段执行 timer（**setTimeout、setInterval**）的回调
- I/O callbacks 阶段：处理一些上一轮循环中的少数未执行的 I/O 回调
- idle, prepare 阶段：仅 node 内部使用
- poll 阶段：获取新的 I/O 事件, 适当的条件下 node 将阻塞在这里
- check 阶段：执行 **setImmediate()** 的回调
- close callbacks 阶段：执行 socket 的 close 事件回调

注意：**上面六个阶段都不包括 process.nextTick()**。

#### 例子

```javascript
setTimeout(()=>{
    console.log('timer1')
    Promise.resolve().then(function() {
        console.log('promise1')
    })
}, 0)
setTimeout(()=>{
    console.log('timer2')
    Promise.resolve().then(function() {
        console.log('promise2')
    })
}, 0)
```

- 浏览器端运行结果：`timer1=>promise1=>timer2=>promise2`
- Node 端运行结果：`timer1=>timer2=>promise1=>promise2`

#### 总结

浏览器和 Node 环境下，microtask 任务队列的执行时机不同：

- Node 端，microtask 在事件循环的各个阶段之间执行；
- 浏览器端，microtask 在事件循环的 macrotask 执行完之后执行。