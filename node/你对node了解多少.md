### 你对node了解多少

参考：[NodeJS有难度的面试题，你能答对几个？](https://juejin.cn/post/6844903951742025736)、https://elemefe.github.io/node-interview/#/sections/zh-cn/

node性能优化：[你不知道的Node.js性能优化](https://zhuanlan.zhihu.com/p/50055740)

#### 前言

##### （1）概念

 `Node.js` 就是一个服务器端的、非阻塞式I/O的、事件驱动的`JavaScript`运行环境。

##### （2）优缺点

优点：

- 处理**高并发**场景性能更佳
- 适合**I/O密集型应用**，值的是应用在运行极限时，CPU占用率仍然比较低，大部分时间是在做 I/O硬盘内存读写操作

因为`Nodejs`是**单线程**，带来的缺点有：

- **不适合CPU密集型应用**
- 只支持单核CPU，不能充分利用CPU
- 可靠性低，一旦代码某个环节崩溃，整个系统都崩溃

##### （3）应用场景

借助`Nodejs`的特点和弊端，其应用场景分类如下：

- 善于`I/O`，不善于计算。因为Nodejs是一个单线程，如果计算（同步）太多，则会阻塞这个线程
- **大量并发的I/O**，应用程序内部并不需要进行非常复杂的处理
- 与 **websocket 配合**，开发长连接的实时交互应用程序

具体场景可以表现为如下：

- 第一大类：用户表单收集系统、后台管理系统、实时交互系统、考试系统、联网软件、高并发量的web应用程序
- 第二大类：基于web、canvas等多人联网游戏
- 第三大类：基于web的多人实时聊天客户端、聊天室、图文直播
- 第四大类：单页面浏览器应用程序
- 第五大类：操作数据库、为前端和移动端提供基于`json`的API

#### 1、node模块机制

##### （1）暴露模块

##### `module.exports = value`或`exports.xxx = value`

- **CommonJS暴露的模块到底是什么?**
- CommonJS规范规定，每个模块内部，module变量代表当前模块。这个变量是一个**对象**，它的**exports属性**（即module.exports，也是一个对象）是对外的接口。**加载某个模块，其实是加载该模块的module.exports属性**。 

##### （2）引入模块

`require(xxx)`，如果是第三方模块，xxx为模块名；如果是自定义模块，xxx为模块文件路径

- require 其实内部调用 Module._load 方法 

- require命令的基本功能是，**读入并执行一个JavaScript文件，然后返回该模块的exports对象**。如果没有发现指定模块，会报错。 
- 步骤：
  - （1）计算模块路径
  - （2）如果模块在缓存里面，取出缓存
  - （3）如果为内置模块，直接require引用
  - （4）生成模块实例module，并存入缓存
  - （5）module.load ()加载模块
  - （6）输出模块 module的exports属性

```javascript
// require 其实内部调用 Module._load 方法
Module._load = function(request, parent, isMain) {
  //  计算绝对路径
  var filename = Module._resolveFilename(request, parent);

  //  第一步：如果有缓存，取出缓存
  var cachedModule = Module._cache[filename];
  if (cachedModule) {
    return cachedModule.exports;

  // 第二步：是否为内置模块
  if (NativeModule.exists(filename)) {
    return NativeModule.require(filename);
  }
  
  /********************************这里注意了**************************/
  // 第三步：生成模块实例，存入缓存
  // 这里的Module就是我们上面的1.1定义的Module
  var module = new Module(filename, parent);
  Module._cache[filename] = module;

  /********************************这里注意了**************************/
  // 第四步：加载模块
  // 下面的module.load实际上是Module原型上有一个方法叫Module.prototype.load
  try {
    module.load(filename);
    hadException = false;
  } finally {
    if (hadException) {
      delete Module._cache[filename];
    }
  }

  // 第五步：输出模块的exports属性
  return module.exports;
};
```



#### 2、异步I/O



#### 3、v8的垃圾回收机制

##### （1）如何查看V8的内存使用情况

使用`process.memoryUsage()`，返回如下

```javascript
{
  rss: 4935680,
  heapTotal: 1826816,
  heapUsed: 650472,
  external: 49879
}
```

heapTotal 和 heapUsed 代表V8的内存使用情况。 external代表V8管理的，绑定到Javascript的C++对象的内存使用情况。 rss, 驻留集大小, 是给这个进程分配了多少物理内存(占总分配内存的一部分) 这些物理内存中包含堆，栈，和代码段。

##### （2）V8的内存限制是多少，为什么V8这样设计

64位系统下是1.4GB， 32位系统下是0.7GB。因为1.5GB的垃圾回收堆内存，V8需要花费50毫秒以上，做一次非增量式的垃圾回收甚至要1秒以上。这是垃圾回收中引起Javascript线程暂停执行的事件，在这样的花销下，应用的性能和影响力都会直线下降。

##### （3）V8的内存分代和回收算法

在V8中，主要将内存分为**新生代**和**老生代**两代。新生代中的**对象存活时间较短**的对象，老生代中的对象存活时间较长，或常驻内存的对象。

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/22/16c17670e2c0fcbd~tplv-t2oaga2asx-watermark.image)

###### 1）新生代

新生代中的对象主要通过**Scavenge算法**进行垃圾回收。这是一种采用复制的方式实现的垃圾回收算法。它将堆内存一份为二，每一部分空间成为semispace。在这两个semispace空间中，只有一个处于使用中，另一个处于闲置状态。处于使用状态的semispace空间称为**From空间**，处于闲置状态的空间称为**To空间**。

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/22/16c176726dc85756~tplv-t2oaga2asx-watermark.image)

- 当开始垃圾回收的时候，会检查From空间中的**存活对象**，这些存活对象将被复制到To空间中，而非存活对象占用的空间将会被释放。完成复制后，From空间和To空间发生角色对换。
- 应为新生代中对象的生命周期比较短，就比较适合这个算法。
- 当一个对象经过多次复制依然存活，它将会被认为是生命周期较长的对象。这种新生代中生命周期较长的对象随后会被移到老生代中。

###### 2）老生代

老生代主要采取的是**标记清除**的垃圾回收算法。与Scavenge复制活着的对象不同，标记清除算法在标记阶段遍历堆中的所有对象，并标记活着的对象，只清理死亡对象。活对象在新生代中只占叫小部分，死对象在老生代中只占较小部分，这是为什么采用标记清除算法的原因。

###### 3）标记清楚算法的问题

主要问题是每一次进行标记清除回收后，内存空间会出现不连续的状态



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/22/16c176daf6c88bc8~tplv-t2oaga2asx-watermark.image)



- 这种内存碎片会对后续内存分配造成问题，很可能出现需要分配一个大对象的情况，这时所有的碎片空间都无法完成此次分配，就会提前触发垃圾回收，而这次回收是不必要的。
- 为了解决碎片问题，标记整理被提出来。就是在对象被标记死亡后，在整理的过程中，将活着的对象往一端移动，移动完成后，直接清理掉边界外的内存。

###### 4）内存泄露的几种情况

**①全局变量**

**②闭包**

```javascript
function out() {
  const bigData = new Buffer(100);
  inner = function () {
    void bigData;
  }
}
```

- inner 直接挂在了  root 上，从而导致内存泄漏（bigData 不会释放）。

**③事件监听**

重复监听导致内存泄漏。

###### 4）如何排查内存泄漏

- ESLint 检测代码检查非期望的**全局变量**。
- 使用闭包的时候，得知道闭包了什么对象，还有引用闭包的对象**何时清除闭包**。最好可以避免写出复杂的闭包，因为复杂的闭包引起的内存泄漏，如果没有打印内存快照的话，是很难看出来的。
- 绑定事件的时候，一定得在恰当的时候**清除事件**。在编写一个类的时候，推荐使用 init 函数对类的事件监听进行绑定和资源申请，然后 destroy 函数对事件和占用资源进行释放。

#### 4、buffer模块

##### （1）新建Buffer会占用V8分配的内存吗

不会，Buffer属于**堆外内存**，不是V8分配的。

##### （2）Buffer.alloc和Buffer.allocUnsafe的区别

Buffer.allocUnsafe创建的 Buffer 实例的底层内存是未初始化的。 新创建的 Buffer 的内容是未知的，可能包含敏感数据。 使用 Buffer.alloc() 可以创建以零初始化的 Buffer 实例。

##### （3）Buffer的内存分配机制

为了高效的使用申请来的内存，Node采用了**slab分配机制**。slab是一种动态的内存管理机制。 Node以8kb为界限来来区分Buffer为大对象还是小对象，如果是小于8kb就是小Buffer，大于8kb就是大Buffer。

#### 5、websocket

##### （1）webSocket与传统的http有什么优势

- 客户端与服务器只需要一个TCP连接，比http长轮询使用更少的连接
- webSocket服务端可以推送数据到客户端
- 更轻量的协议头，减少数据传输量

##### （2）webSocket协议升级时什么，能简述一下吗？

首先，WebSocket连接必须由浏览器发起，因为请求协议是一个标准的HTTP请求，格式如下：

```
GET ws://localhost:3000/ws/chat HTTP/1.1
Host: localhost
Upgrade: websocket
Connection: Upgrade
Origin: http://localhost:3000
Sec-WebSocket-Key: client-random-string
Sec-WebSocket-Version: 13
```

该请求和普通的HTTP请求有几点不同：

- GET请求的地址不是类似/path/，而是以ws://开头的地址；
- 请求头Upgrade: websocket和Connection: Upgrade表示这个连接将要被转换为WebSocket连接；
- Sec-WebSocket-Key是用于标识这个连接，并非用于加密数据；
- Sec-WebSocket-Version指定了WebSocket的协议版本。

随后，服务器如果接受该请求，就会返回如下响应：

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: server-random-string
```

该响应代码101表示本次连接的HTTP协议即将被更改，更改后的协议就是Upgrade: websocket指定的WebSocket协议。



#### 6、中间件