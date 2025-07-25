Event Loop即事件循环，是指浏览器或Node的一种解决javaScript**单线程**运行时不会阻塞的一种机制，也就是我们经常使用**异步**的原理。

#### 任务
同步任务（synchronous）：
- 在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务。

异步任务（asynchronous）：
- 不进入主线程，而进入 **"任务队列"（task queue）** 的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

#### 异步任务的运行机制
- 所有同步任务都在主线程上执行，形成一个**执行栈**（execution context stack）。

- 主线程之外，还存在一个 **"任务队列"（task queue）**。只要异步任务有了运行结果，就在"任务队列"之中放置一个**事件**。

- 一旦"执行栈"中的**所有同步任务执行完毕，系统就会读取"任务队列"**，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。

- 主线程不断重复上面的第三步。

**【补充】**

堆（Heap）：

堆是一种数据结构，是利用**完全二叉树**维护的一组数据（相当于一维数组），堆分为两种，一种为最大堆（根节点最大的堆），一种为最小堆（根节点最小的堆）。

#### 宏任务与微任务（均为异步任务）
MacroTask（宏任务，简称Task）：

- `script`全部代码、`setTimeout`、`setInterval`、`setImmediate`（浏览器暂时不支持，只有IE10支持，具体可见MDN）、DOM事件（点击/滚动等）、AJAX请求、`I/O`（文件读写/网络请求）、`requestAnimationFrame`（浏览器动画帧回调）、`UI Rendering`。


MicroTask（微任务）：

 - `Process.nextTick`（Node独有）、`Promise.then()`、`async/await`、`Object.observe`(废弃)、`MutationObserver`（监听 DOM 变化）、`queueMicrotask()` 显式添加的微任务。


任务的优先级：

-  `Process.nextTick`>`Promise.then()`>`setTimeout`>`setImmediate`

**【注意】** promise虽然是异步函数，但promise里面的代码同步执行，`promise.then()`里面的代码才需要异步执行，属于微任务。

#### 怎么识别宏任务和微任务

宏任务：

- 由**宿主（浏览器、Node）**发起的，一般在JavaScript引擎空闲时执行（不知道这样说合不合理）。

- 任务大耗时但不紧急。

微任务：

- 由**JavaScript引擎线程自身**发起的轻量级异步任务，需在当前宏任务结束后立即执行，优先级高于宏任务。
- 指在当前任务执行结束后立即执行的任务。
- 任务小而紧急。

> 所以发起一个请求，属于宏任务，因为是由浏览器发起的。

这里涉及浏览器进程和线程的概念：

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/54a222107e4949dc8af23a02ea3ed484~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=707&h=387&s=48843&e=png&b=f8f3f3)

#### 执行顺序

- 执行同步代码
- 执行当前队列所有的微任务
- 执行一个宏任务（**每次只会触发一次宏任务**）
- 依次菜重复步骤2和3

#### 事件循环机制

`main thread` 主线程和 `call-stack` 调用栈(执行栈)，所有的任务都会被放到调用栈等待主线程执行。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210201093030362.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210201093050923.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)

**任务队列：**

"任务队列"是一个事件的队列（消息队列），IO设备完成一项任务，就在"任务队列"中添加一个**事件**（IO设备的事件、用户产生的事件（比如鼠标点击、页面滚动等）），表示相关的异步任务可以进入"执行栈"了。主线程读取"任务队列"，就是读取里面有哪些事件。

所谓 **"回调函数"（callback）**，就是那些会被主线程挂起来的代码。

异步任务必须指定回调函数，当主线程开始执行异步任务，就是执行对应的回调函数。

因为异步任务中的 **微任务优先级大于宏任务**，又可以把 **任务队列** 细分为 **消息队列** 和 **微任务队列** ，分别存放宏任务和微任务，在同步任务执行完毕后，再依次执行微任务、宏任务。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210201094348881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)


**执行顺序：**
- 同步任务（调用栈或执行栈）->微任务（微任务队列）->DOM渲染 ->宏任务（消息队列）
- 因为宏任务和微任务都是存放在队列中的，所以是**先进先出**

**【补充】**

回调函数与闭包：
- 回调函数：函数可以作为另一个函数的**参数**（异步请求），`promise()`函数（异步函数）用于解决回调地狱问题
- 闭包：函数可以作为另一个函数的**返回值**

#### 面试题
例1：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210201094901982.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
- 程序从上往下执行，`setTimeout()`属于宏任务，被压入消息队列；

- new一个对象promise是瞬间执行的，所以先打印出3和4；

- 程序继续往下执行，遇到`promise.then()`，压入微任务队列，继续往下`console.log(8)`打印出8；

- `promise.then()`优先级大于`setTimeout()`，所以会先从微任务队列中取出`promise.then()`执行`console.log(5)`打印出5，接着执行`setTimeout()`里面的代码打印出2；

- 综上，打印结果为34852。

例2：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210201094910227.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)

例3：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210201095616166.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)

例4：

![在这里插入图片描述\](https://img-blog.csdnimg.cn/20210201095629304.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow](https://img-blog.csdnimg.cn/20210201095649741.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
- new一个对象会瞬时执行，函数需要调用（即最后一行`fun2()`被调用后）才会执行
- 参考视频教程：[2分钟了解 JavaScript Event Loop | 面试必备](https://www.bilibili.com/video/BV1kf4y1U7Ln/?spm_id_from=333.788.b_636f6d6d656e74.30)

例5:

```js
//S1
setTimeout(() => {
  console.log(1); 
  //S4
  setTimeout(() => {
    //M2
    Promise.resolve().then(() => {
      console.log(9);
    }); 
  }, 0);
  //M3
  Promise.resolve().then(() => {
    console.log(7);
  }); 
}, 0); 

console.log(2); 

//M1
Promise.resolve().then(() => {
  console.log(3);
}); 

//S2
setTimeout(() => {
  console.log(8);
  //S5
  setTimeout(() => {
    console.log(5);
  }, 0); 
}, 0);

//S3
setTimeout(() => {
  //M6
  Promise.resolve().then(() => {
    console.log(4);
  }); 
}, 0);

console.log(6);
```

输出顺序: 2 6 3 1 7 8 4 9 5

**每次从宏任务队列里面取宏任务时，只取一个；而从微任务队列里面取微任务时，取全部。**





参考博客：[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)、[一次弄懂Event Loop](https://zhuanlan.zhihu.com/p/55511602)、[关于 JavaScript 的事件循环、微任务和宏任务](https://blog.csdn.net/qq_41950190/article/details/130076969)