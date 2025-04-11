#### 初识

```javascript
// promise简单结构
const isPregnant = true;

// 在new一个promise对象的时候，需要传递的参数是一个回调函数
const promise = new Promise((resolve,reject)=>{
	if(isPregnant){
		resolve('孩子他爹'); // 如果是resolve则调用then里面的内容
	}else{
		reject('老公');
	}
});

promise
.then(name=>{
	console.log(`男人成为了${name}。`);
})
.catch(name=>{
	console.log(`男人成为了${name}。`);
})
.finally(()=>{
	console.log('男人和女人最终结婚了。')
})

// 打印：
// 男人成为了孩子他爹。
// 男人和女人最终结婚了。
```

#### 三种状态

pending，resolved，rejected

- 当Promise刚创建完成时，处于pending状态；
- 当Promise中的函数参数执行了resolve后，Promise由pending状态变成resolved状态；
- 如果在Promise的函数参数中执行的不是resolve方法，而是reject方法，那么Promise会由pending状态变成rejected状态。

#### 方法

##### 1、new Promise()

```javascript
const promise = new Promise((resolve,reject)=>{
    ...
});
```

执行器函数接受2个参数，分别是resolve函数和reject函数。

##### 2、Promise.resolve()

返回一个带着给定值解析过的`Promise`对象，如果参数本身就是一个`Promise`对象，则直接返回这个`Promise`对象。 

```javascript
let p1 = new Promise((resolve, reject) => {
    resolve(1)
});
let p2 = Promise.resolve(p1);
let p3 = Promise.resolve(2);
console.log(p1) // Promise { <state>: "fulfilled", <value>: 1 }
console.log(p2) // Promise { <state>: "fulfilled", <value>: 1 }
console.log(p3) // Promise { <state>: "fulfilled", <value>: 2 }
console.log(p1==p2) // true
```

（1）如果这个值是个 thenable（即带有 then 方法），返回的 Promise 对象会“跟随”这个 thenable 的对象，采用它的最终状态（指 resolved/rejected/pending/settled）。

（2）如果传入的 value 本身就是 Promise 对象，则该对象**作为 Promise.resolve 方法的返回值**返回。

（3）其他情况以该值为成功状态返回一个 Promise 对象。

##### 3、Promise.reject()

返回一个带有拒绝原因的`Promise`对象。 

同Promise.resolve()。

```javascript
let p1 = new Promise((resolve, reject) => {
    resolve(1)
});
let p2 = Promise.reject(p1);
let p3 = Promise.reject(2);
console.log(p1) // Promise { <state>: "fulfilled", <reason>: 1 }
console.log(p2) // Promise { <state>: "rejected", <reason>: Promise { "fulfilled" } }
console.log(p3) // Promise { <state>: "rejected", <reason>: 2 }
console.log(p1==p2) // false
```

##### 4、Promise.all()

Promise.all()方法提供了**并行执行异步操作**的能力，并且**在所有异步操作执行完后才执行回调**。

接收一个promise的iterable类型（注：Array，Map，Set都属于ES6的iterable类型）的输入，并且只返回一个[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)实例， 那个输入的所有promise的resolve回调的结果是一个**数组**。

**resolve回调执行**是，在所有输入的promise的resolve回调都结束，或者输入的iterable里没有promise了的时候。

**reject回调执行**是，只要任何一个输入的promise的reject回调执行或者输入不合法的promise就会立即抛出错误，并且reject的是第一个抛出的错误信息。

应用场景：

- 有一个场景是很适合用这个的，一些游戏类的素材比较多的应用，打开网页时，预先加载需要用到的各种资源如图片、flash以及各种静态文件。所有的都加载完后，我们再进行页面的初始化。
- 初始化bundle：[多个promise并发执行，如果某个promise失败，则尝试重新执行该promise一次，如果还是失败则提示错误](https://blog.csdn.net/weixin_43973415/article/details/132451331)

简单例子：

```javascript
const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 'foo');
});

Promise.all([promise1, promise2, promise3]).then((values) => {
  console.log(values);
});
// 输出： Array [3, 42, "foo"]
```

[`Promise.all` 的异步和同步](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/all#promise.all_的异步和同步)

- 异步性

  ```javascript
  var resolvedPromisesArray = [Promise.resolve(33), Promise.resolve(44)];
  
  var p = Promise.all(resolvedPromisesArray);
  // immediately logging the value of p
  console.log(p);
  
  // using setTimeout we can execute code after the stack is empty
  setTimeout(function(){
      console.log('the stack is now empty');
      console.log(p);
  });
  
  // logs, in order:
  // Promise { <state>: "pending" }
  // the stack is now empty
  // Promise { <state>: "fulfilled", <value>: Array[2] }
  ```

  - 其它情况下返回一个**处理中（pending）**的[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)。这个返回的 `promise` 之后会在所有的 `promise` 都完成或有一个 `promise` 失败时**异步**地变为完成或失败。 

- 同步性

  ```javascript
  var p = Promise.all([]); // will be immediately resolved
  var p2 = Promise.all([1337, "hi"]); // non-promise values will be ignored, but the evaluation will be done asynchronously
  console.log(p);
  console.log(p2)
  setTimeout(function(){
      console.log('the stack is now empty');
      console.log(p2);
  });
  
  // logs
  // Promise { <state>: "fulfilled", <value>: Array[0] }
  // Promise { <state>: "pending" }
  // the stack is now empty
  // Promise { <state>: "fulfilled", <value>: Array[2] }
  ```

  - 如果传入的参数是一个空的可迭代对象，则返回一个**已完成（already resolved）**状态的 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)。对应p
  -  如果传入的参数不包含任何 `promise`，则返回一个**异步完成（asynchronously resolved，先pending，再resolved）** [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)。 对应p2

promise.all如果有一个失败的话，就会直接返回失败的promise，其他promise还会执行吗？

- 会执行

```js
const resolvePromise = (time) =>
  new Promise((resolve, reject) => {
    setTimeout(() => resolve(console.log("resolve:", time)), time * 1000);
  });
const rejectPromise = (time) =>
  new Promise((resolve, reject) => {
    setTimeout(() => reject(console.log("reject:", time)), time * 1000);
  });
const p1 = resolvePromise(1);
const p2 = resolvePromise(3); // 后续的promise还会继续执行
const p3 = rejectPromise(2);

Promise.all([p1, p2, p3])
  .then((res) => console.log("promise all resolve:", res))
  .catch((e) => {
    console.log("promise all reject:", e);
  });

// resolve:1
// reject:2
// promise all reject: undfined
// resolve:3
```

##### 5、Promise.allSettled()

接受一个 **Promise** 对象的数组作为参数，并返回一个新的 Promise 实例。

该实例会在所有传入的 Promise 实例都完成(**无论是 resolved 还是 rejected**)**后才完成**。

返回的promise是一个对象数组，每一个对象包含下面三个值：

- status：一个字符串，要么是 `"fulfilled"`，要么是 `"rejected"`，表示 promise 的最终状态。
- value：仅当 `status` 为 `"fulfilled"`，才存在。promise 兑现的值。
- reason：仅当 `status` 为 `"rejected"`，才存在，promsie 拒绝的原因。

```js
  const promise1 = new Promise((resolve, reject) => {
    setTimeout(() => resolve('Promise 1 resolved'), 1000)
  })

  const promise2 = new Promise((resolve, reject) => {
    setTimeout(() => reject('Promise 2 rejected'), 500)
  })

  const promise3 = new Promise((resolve, reject) => {
    setTimeout(() => resolve('Promise 3 resolved'), 1500)
  })

  // 使用 Promise.allSettled
  Promise.allSettled([promise1, promise2, promise3])
    .then((results) => console.log('result:',results))
    .catch((error) => console.error('error:',error))
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/62ccb23d8d8e44d188cddd6e8a548428~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=789&h=361&s=31250&e=png&b=ffffff)

与Promise.all()的区别：

- Promise.all() 在其中任何一个 Promise 被拒绝时就会被拒绝，而 Promise.allSettled() 则会在所有 Promise 实例都完成(无论成功还是失败)时才完成。

- Promise.all() 返回的是一个数组，数组中的值是传入的 Promise 实例的结果值,而 Promise.allSettled() 返回的是一个数组，数组中的值是对象，每个对象表示对应的 Promise 实例的状态和结果值。

- 当你需要获取所有异步操作的结果时，无论是成功还是失败，应该使用 Promise.allSettled()。如果只关心所有操作都成功的情况，可以使用 Promise.all()。

##### 6、Promise.race()

返回一个 promise，一旦迭代器中的某个promise解决或拒绝，返回的 promise就会解决或拒绝。

```javascript
var resolvedPromisesArray = [Promise.resolve(33), Promise.resolve(44)];

var p = Promise.race(resolvedPromisesArray);
// immediately logging the value of p
console.log(p);

// using setTimeout we can execute code after the stack is empty
setTimeout(function(){
    console.log('the stack is now empty');
    console.log(p);
});

// 输出:
// Promise { <state>: "pending" }
// the stack is now empty
// Promise { <state>: "fulfilled", <value>: 33 }
```

应用场景：

- 比如我们可以用race**给某个异步请求设置超时时间，并且在超时后执行相应的操作**，代码如下：

```javascript
 //请求某个图片资源
    function requestImg(){
        var p = new Promise((resolve, reject) => {
            var img = new Image();
            img.onload = function(){
                resolve(img);
            }
            img.src = '图片的路径';
        });
        return p;
    }
    //延时函数，用于给请求计时
    function timeout(){
        var p = new Promise((resolve, reject) => {
            setTimeout(() => {
                reject('图片请求超时');
            }, 5000);
        });
        return p;
    }
    Promise.race([requestImg(), timeout()]).then((data) =>{
        console.log(data);
    }).catch((err) => {
        console.log(err);
    });
```

requestImg函数会异步请求一张图片，我把地址写为"图片的路径"，所以肯定是无法成功请求到的。timeout函数是一个延时5秒的异步操作。我们把这两个返回Promise对象的函数放进race，于是他俩就会赛跑，如果5秒之内图片请求成功了，那么遍进入then方法，执行正常的流程。如果5秒钟图片还未成功返回，那么timeout就跑赢了，则进入catch，报出“图片请求超时”的信息。运行结果如下：

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/5/19/16376a95ffa3b13c~tplv-t2oaga2asx-watermark.image)

##### 7、Promise.any()

接收一个[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)可迭代对象（注：Array，Map，Set都属于ES6的iterable类型），只要其中的一个 `promise` 成功，就返回那个已经成功的 `promise` 。

如果可迭代对象中没有一个 `promise` 成功（即所有的 `promises` 都失败/拒绝），就返回一个失败的 `promise `和[`AggregateError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/AggregateError)类型的实例，它是 [`Error`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Error) 的一个子类，用于把单一的错误集合在一起。

本质上，这个方法和[`Promise.all()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)是相反的。 

##### 8、Promise.prototype.then()

接受resolve()回调的参数值。then 中的函数一定要 return 一个结果或者一个新的 Promise 对象，才可以让之后的then 回调接收。

如果 `then` 中的回调函数：

- 返回了一个值，那么 `then` 返回的 Promise 将会成为接受状态，并且将返回的值作为接受状态的回调函数的参数值。
- 没有返回任何值，那么 `then` 返回的 Promise 将会成为接受状态，并且该接受状态的回调函数的参数值为 `undefined`。
- 抛出一个错误，那么 `then` 返回的 Promise 将会成为拒绝状态，并且将抛出的错误作为拒绝状态的回调函数的参数值。
- 返回一个已经是接受状态的 Promise，那么 `then` 返回的 Promise 也会成为接受状态，并且将那个 Promise 的接受状态的回调函数的参数值作为该被返回的Promise的接受状态回调函数的参数值。
- 返回一个已经是拒绝状态的 Promise，那么 `then` 返回的 Promise 也会成为拒绝状态，并且将那个 Promise 的拒绝状态的回调函数的参数值作为该被返回的Promise的拒绝状态回调函数的参数值。
- 返回一个未定状态（`pending`）的 Promise，那么 `then` 返回 Promise 的状态也是未定的，并且它的终态与那个 Promise 的终态相同；同时，它变为终态时调用的回调函数参数与那个 Promise 变为终态时的回调函数的参数是相同的。

##### 9、Promise.prototype.catch()

##### 10、Promise.prototype.finally()

参考博客：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise

#### 迭代器

遍历与迭代的区别：从目标源依次逐个抽取的方式来提取数据。（目标源必须是有序的且连续的）

#### async、await、promise三者关系

##### 1、执行async函数，返回的都是promise对象

```javascript
async function test1(){
    return 1; // 被封装成promise对象再返回
}
async function test2(){
    return Promise.resolve(2);
}

const result1 = test1();
const result2 = test2();
console.log('result1', result1); // result1 Promise {<fulfilled>: 1}
console.log('result2', result2); // result2 Promise {<pending>}

// 也可以使用箭头函数
let hello = async () => { return "Hello" };
hello().then((value) => console.log(value));
// 也可以简写成：hello().then(console.log);
```

![1626592675375](C:\Users\lhq\AppData\Roaming\Typora\typora-user-images\1626592675375.png)

-  `async` 函数内部 return 返回的值。会成为 `then` 方法回调函数的参数 。
-  如果 `async` 函数内部抛出异常，则会导致返回的 Promise 对象状态变为 `reject` 状态。抛出的错误而会被 `catch` 方法回调函数接收到。 
-  异步函数的特征之一：它保证函数的返回值为 promise。

##### 2、promise.then成功的情况对应await

await后面跟一个promise对象：会等待该promise resolve或者reject

await后面跟一个数字：直接转换成resolve，相当于await Promise.resolve(4)

await后面跟一个async函数：会转换成resolve的promise

```javascript
// await后面跟一个promise对象
async function test3(){
    const p3 = Promise.resolve(3); // 定义一个promise对象，并赋值给p3
    
    p3.then(data3 => {
        console.log('data3', data3); // data3 3
    })
    
    // 等同于下面的代码
    const data4 = await p3;
    console.log('data4', data4); // data4 3
}

test3()
```

```javascript
// await后面跟一个数字，会被立即转换成resolved状态的promise对象
async function test4(){
    const data4 = await 4; // 相当于 await Promise.resolve(4)
    console.log('data4', data4); // data4 4
}

test4()
```

```javascript
// await后面跟一个异步函数async
async function test5(){
    const data5 = await test1(); // async异步函数test1()返回的是一个promise对象
    console.log('data5', data5); // data5 1
}

test5()
```

```javascript
// await后面跟一个promise对象
async function test3(){
    const p1 = Promise.resolve(1); // 定义一个promise对象，并赋值给p1
    const p2 = Promise.reject(2);
    
    const data2 = await p2;
    const data1 = await p1;
    console.log('data1, data2', data1, data2); // data4 3
}

test3()
```



**async 函数返回的 Promise 对象，必须等到内部所有的 await 命令的 Promise 对象执行完，才会发生状态改变**。

也就是说，只有当 `async` 函数内部的异步操作都执行完，才会执行 `then` 方法的回调。

```javascript
const delay = timeout => new Promise(resolve=> setTimeout(resolve, timeout));
async function f(){
    await delay(1000);
    await delay(2000);
    await delay(3000);
    return 'done';
}

f().then(v => console.log(v)); // 等待6s后才输出 'done'
```

##### 3、promise.catch异常的情况对应try...catch

```javascript
async function test6(){
    const p6 = Promise.reject(6); // 定义一个promise对象，并赋值给p6
    try{
        const data6 = await p6;
        console.log('data6', data6);
    }catch(e){
        console.error('e',e)
    }
}

test6() // e 6
```

![1626594661029](C:\Users\lhq\AppData\Roaming\Typora\typora-user-images\1626594661029.png)

await 命令后面的 Promise 对象，运行结果可能是 rejected，所以最好把 await 命令放在 try...catch 代码块中。

有两个例子需要好好看看：[async和await:让异步编程更简单](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Asynchronous/Async_await)、[优雅的异步处理](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Asynchronous/Promises)

#### 常见面试题

1、Promise有几种状态?

> pending，resolved，rejected

2、Promise的状态是否可变?

> 不可变。
>
> Promise状态的一旦变成resolved或rejected时，Promise的状态和值就固定下来了，不论后续再怎么调用resolve或reject方法，都不能改变它的状态和值。

3、Promise如何解决地域回调?

> then里面可以return Promise，来防止地狱回调。（链式调用）

4、Promise有哪些方法? 他们的应用场景是什么?（all/race）

> 应用场景：当网络请求慢于2s的时候，就使用race方法来提示网络状况不佳

5、如何让Promise顺序执行?（async/await）

>then链式调用，async/await，promise.all()

6、常与setTimeout结合考宏任务、微任务执行顺序：promise接收的函数参数是同步执行的，但then方法中的回调函数执行则是异步的。

7、为什么promise中的执行器函数是同步执行，而then()是异步执行？

> Promise是异步动作的同步处理方案。
>
> 执行器函数一般是为了**启动这些异步操作**，比如网络请求、下载文件、native sdk等；
>
> then异步执行是为了等待状态发生变化，进行可以做出对应操作，以及为了不足塞后续代码的执行。

#### 使用总结

- 首先初始化一个 Promise 对象，可以通过两种方式创建， 这两种方式都会返回一个 Promise 对象。

  - new Promise(fn)
  - Promise.resolve(fn)

- 然后调用上一步返回的 promise 对象的 then 方法，注册回调函数。

  - then 中的回调函数可以有一个参数，也可以不带参数。**如果 then 中的回调函数依赖上一步的返回结果，那么要带上参数。**then 中的函数一定要 return 一个结果或者一个新的 Promise 对象，才可以让之后的then 回调接收。比如

  ```javascript
      new Promise(fn)
      .then(fn1(value）{
          //处理value
      })
  ```

- 最后注册 catch 异常处理函数，处理前面回调中可能抛出的异常。

通常按照这三个步骤，你就能够应对绝大部分的异步处理场景。

简单例子：

```javascript
// 告诉保姆帮我做几件连贯的事情，先去超市买菜：

// 方法一
new Promise(买菜)
//用买好的菜做饭
.then((买好的菜)=>{
    return new Promise(做饭);
})
//把做好的饭送到老婆公司
.then((做好的饭)=>{
    return new Promise(送饭);
})
//送完饭后打电话通知我
.then((送饭结果)=>{
    电话通知我();
})

// 方法二
(async ()=>{
    let 蔬菜 = await 买菜();
    let 饭菜 = await 做饭(蔬菜);
    let 送饭结果 = await 送饭(饭菜);
    let 通知结果 = await 通知我(送饭结果);
})();
```



#### 编程题

##### 1、每隔1秒输出1,2,3

```javascript
const arr = [1, 2, 3]
// 方法一
arr.reduce((p, x) => {
  return p.then(() => {
    return new Promise(r => {
      setTimeout(() => r(console.log(x)), 1000)
    })
  })
}, Promise.resolve())

// 方法二
arr.forEach((number, index) => {
    setTimeout(() => {
        console.log(number);
    }, 1000 * (index + 1));
});

// 方法三
const delay = (n) => new Promise(r => setTimeout(() =>r(console.log(n)),1000))
const func= async(arr)=>{
    for(let i=0; i<arr.length; i++){
        await delay(arr[i]);
    }
}
func([1,2,3])

// forEach是不可以的，会同步执行完所有sync，导致1s后同时打印1，2，3
arr.forEach(async(n)=>{
   await delay(n);
})
```

如何让异步操作顺序执行？

- 一定要记得 new Promise(executor) 的 executor 是马上执行的。

- 所以要么你递增 timeout 的时间，要么在一个 Promise resolved 之后再创建新的 Promise。

##### 2、红绿灯交替重复亮

红灯3秒亮一次，黄灯2秒亮一次，绿灯1秒亮一次；如何让三个灯不断交替重复亮灯？

（用Promise实现）三个亮灯函数已经存在：

```javascript
function red() {
  console.log("red");
}
function green() {
  console.log("green");
}
function yellow() {
  console.log("yellow");
}
const light = function (timer, cb) {
  return new Promise(resolve => {
    setTimeout(() => {
      cb()
      resolve()
    }, timer)
  })
}
/* 方法一：
const step = function () {
  Promise.resolve().then(() => {
    return light(3000, red) // 返回一个promise对象，以便后面的then可以执行
  }).then(() => {
    return light(2000, green)
  }).then(() => {
    return light(1000, yellow)
  }).then(() => {
    return step()
  })
}
*/
// 方法二：
const step = async function () {
    await light(3000, red)
    await light(2000, green)
    await light(1000, yellow)
    step()
}

step();
```

##### 3、美团面试题

func1 func2 func3如果顺序运行的话，打印出来是2 1 3 ，要求实现一下那个excute方法，使得打印结果成为 1 2 3。

```javascript
function cb(value){
    console.log(value)
}

function func1(cb) {
    setTimeout(() => {
        cb(1)
    }, 2000)
}

function func2(cb) {
    setTimeout(() => {
        cb(2)
    }, 1000)
}

function func3(cb) {
    setTimeout(() => {
        cb(3)
    }, 3000)
}

let queue = new Queue()
queue.excute(func1)
queue.excute(func2)
queue.excute(func3)
```

代码实现：

```javascript
function cb(value){
    console.log(value)
}

// callback: (value: number) => void;
function func1(callback) {
    setTimeout(() => {
        callback(1)
    }, 2000)
}

function func2(callback) {
    setTimeout(() => {
        callback(2)
    }, 1000)
}

function func3(callback) {
    setTimeout(() => {
        callback(3)
    }, 3000)
}

class Queue {
    constructor() {
        // 初始化一个空数组用于存储要执行的函数
        this.queue = [];
        // 标记当前是否有函数正在执行
        this.isExecuting = false;
    }

    // excute 方法，用于将函数添加到队列中并尝试执行
    excute(func) {
        // 将传入的函数添加到队列末尾
        this.queue.push(func);
        // 如果当前没有函数正在执行，则开始执行队列中的函数
        if (!this.isExecuting) {
            this.processQueue();
        }
    }

    // 处理队列中的函数
    async processQueue() {
        // 标记为正在执行
        this.isExecuting = true;
        // 当队列不为空时，循环执行队列中的函数
        while (this.queue.length > 0) {
            // 取出队列头部的函数
            const currentFunc = this.queue.shift();
            // 使用 Promise 封装当前函数的执行
            await new Promise((resolve) => {
                // 执行当前函数，并传入一个回调函数
                currentFunc((value) => {
                    console.log('value:', value); // 打印1 2 3
                    // 调用回调函数打印结果
                    cb(value);
                    // 函数执行完毕，标记为已完成
                    resolve();
                });
            });
        }
        // 队列处理完毕，标记为没有函数正在执行
        this.isExecuting = false;
    }
}


let queue = new Queue()
queue.excute(func1)
queue.excute(func2)
queue.excute(func3)
```



##### 4、实现mergePromise函数

实现mergePromise函数，把传进去的数组顺序先后执行，并且把返回的数据先后放到数组data中 。

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>title</title>
</head>
<body>

<script>
    const time = (timer) => {
        return new Promise(resolve => {
            setTimeout(() => {
                resolve()
            }, timer)
        })
    }
    const ajax1 = () => time(3000).then(() => {
        console.log(1);
        return 1
    })
    const ajax2 = () => time(2000).then(() => {
        console.log(2);
        return 2
    })
    const ajax3 = () => time(1000).then(() => {
        console.log(3);
        return 3
    })

    function mergePromise (ajaxArray) {
        // 在这里写代码
        // 存放每个ajax异步操作的结果
        const data = [];
        let promise = Promise.resolve();
        ajaxArray.forEach(ajax => {
            // 第一次的then为了用来调用ajax，此处的ajax是函数，需要调用后才会执行
            // 第二次的then是为了获取ajax的结果，并将结果重新赋值到promise上
            promise = promise.then(ajax).then(res => {
                data.push(res);
                return data; // 把每次的结果返回
            })
        })
        // 最后得到的promise它的值就是data
        return promise;
    }
    
    // 并行执行异步操作
    // Promise.all([ajax1(), ajax2(), ajax3()]).then(data => {
    //     console.log("done");
    //     console.log(data); // data 为 [1, 2, 3]
    // });
    // 输出结果为：
    // 3
    // 2
    // 1
    // done
    // [1, 2, 3]
    
    mergePromise([ajax1, ajax2, ajax3]).then(data => {
        console.log("done");
        console.log(data); // data 为 [1, 2, 3]
    });
    // 要求分别输出：
    // 1
    // 2
    // 3
    // done
    // [1, 2, 3]
</script>
</body>
</html>
```

##### 5、异步加载图片

参考博客：[图片异步加载的几种实现方案](https://www.cnblogs.com/lyraLee/p/11806663.html)

1）串行加载：要求图片依次加载（上一张加载完成，再加载下一张）。

const imgs = [url1, url2, url3,...];

```javascript
async function loadAsync() {
  let promise = function(url) {
    return new Promise((resolve, reject) => {
      const image = new Image();
      image.src = url;
      image.onload = function() {
        document.body.appendChild(image);
        resolve('ok');
      }        
    })      
  }
  for(let url of imgs) {
    await promise(url);
  }
}
loadAsync();
```

2）并行加载：要求图片并行加载，全部加载完成后，再插入DOM。

const imgs = [url1, url2, url3,...];

```javascript
 async function loadAsync() {
  let promise = function(url) {
    return new Promise((resolve, reject) => {
      const image = new Image();
      image.src = url;
      image.onload = function() {
        resolve(image);
      }        
    })      
  }
  const promises = imgs.map(i => promise(i)); //实现并行加载
  const results = await Promise.all(promises);
  results.forEach(image => {
    document.body.appendChild(image)
  });
}
loadAsync();
```

Promise.all()不管执行顺序，并行执行。

##### 6、使用promise，实现一个sleep函数

```javascript
function sleep (time) {
  return new Promise((resolve) => setTimeout(resolve, time));
}

// 异步任务同步执行
async function main(){
    console.log('等待5秒');
    await sleep(5000);
    console.log('5秒过去了');
}
main();

// 另一种方法：按照异步任务执行
sleep(500).then(() => {
    // 这里写sleep之后需要去做的事情
})
```

**另一个实现sleep的场景题：**

Js实现一个 Monkey，返回的对象提供eat、sleep方法

new Monkey('Alan').eat('Banana').sleep(4).eat('Apple').sleep(3).eat('Pear')

要求输出：

my name is Alan

i eat Banana

wait for 4 s

i eat Apple

wait for 3 s

i eat Pear

方法一：

```js
class Monkey {
  constructor(name) {
    console.log(`my name is ${name}`);
  }

  eat(v) {
    console.log(`i eat ${v}`);
    return this;
  }

  // 下面这种写法不行，sleep会返回一个promise的this，无法调后面的eat方法
  //   async sleep(timeout) {
  //     await new Promise((r) => {
  //       setTimeout(() => {
  //         console.log(`wait for ${timeout} s`);
  //         r();
  //       }, timeout * 1000);
  //     });
  //     return this;
  //   }

  sleep(timeout) {
    const nowDate = new Date().getTime();
    while (new Date().getTime() - nowDate < timeout * 1000) {
      // 同步操作，阻塞后续流程
    }
    console.log(`waited for ${timeout} s`);
    return this;
  }
}

new Monkey('Alan').eat('Banana').sleep(4).eat('Apple').sleep(3).eat('Pear');
```

方法二：

```js
class Monkey {
  constructor(name) {
    this.name = name;
    this.queue = Promise.resolve(); // 初始化任务队列（关键点）
    console.log(`my name is ${name}`);
  }

  eat(food) {
    // 在call sleep之后会等待4s后，resolve，然后再执行then里面的方法（关键点）
    this.queue = this.queue.then(() => {
      console.log(`i eat ${food}`);
    });
    return this;
  }

  sleep(seconds) {
    this.queue = this.queue.then(() => {
      return new Promise(resolve => {
        setTimeout(() => {
          console.log(`wait for ${seconds} s`);
          // console.log(this.name) // 箭头函数这里会打印Alan；如果是普通函数，这里会是undefined，此时的this指向全局对象（window和global）
          resolve();
        }, seconds * 1000);
      });
    });
    return this;
  }
}
new Monkey('Alan').eat('Banana').sleep(4).eat('Apple').sleep(3).eat('Pear');
```



完整代码：

```javascript
(async function(){
    /**
    * s 休眠时长， 单位：秒
    */
    function sleep(s){
        s = s || 0;
        s  = parseInt(s) * 1000;
        let now = +new Date(); // 把data转换成number类型，获取当前的时间戳，相当于new Date().getTime()
        let timer = null;
        return new Promise((resolve, reject)=>{
            timer = setInterval(()=>{
                if( now + s < +new Date()){
                    clearInterval(timer);
                    resolve(true);
                }
            }, 10)
        })

    }

    console.log(`start: ${new Date()}`);
    await sleep(3); // 休眠3秒
    console.log(`end  : ${new Date()}`);
})();
```

new前面有个+，其它用法：

```javascript
// null：返回 0
console.info(+null) // => 0

// undefined：返回 NaN
console.info(+undefined) // => NaN

// 获取当前的时间戳，相当于`new Date().getTime()`
console.info(+new Date())

// 布尔型转换为整型：true 返回 1，false 返回 0
console.info(+true) // => 1
console.info(+false) // => 0

// 空字符串：返回0
console.info(+'') // => 0

// 忽略前面的 0
console.info(+'010') // => 10

// 16进制转换成 10进制
console.info(+'0x3E8') // => 1000

// 科学计数法自动解析
console.info(+'1e3') // => 1000
console.info(+'1e-3') // => 0.001

// 无法解析的格式：返回 null
console.info(+'1,000') // => NaN
```

##### 7、手写Promise.all、Promise.race、Promise.any

###### 手写Promise.all

执行一个promise数组，全部resolved则返回数组的执行结果；一旦有个失败的promise，则返回该失败的promise

```javascript
Promise.prototype.all = (iterators) => {
    let arr = Array.from(iterators);
    let result = [];
    let counter = 0; // 设计一个计数器，保存它的成功的数量
    // 1.返回一个promise
    return new Promise((resolve, reject) => {
        arr.forEach((item, index) => {
            Promise.resolve(item).then(value => {
                result[index] = value;
                counter ++;
                if (counter === arr.length) {//通过conter变量比较，而不是直接通过result.length去判断
                    // 2.全部执行完成，则resolve，并返回结果
                    resolve(result);
                }
            }).catch(err => {
                // 3.一旦有一个失败则返回该失败的promise
                reject(err)
            })
        })
    });
}

// 测试
Promise.all([
    new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(1);
        }, 200)
    }),
    new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(2);
        }, 100)
    }),
    new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(3);
        }, 400)
    }),
    new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(4);
        }, 300)
    }),
])
    .then(res => console.log('result:', res))
    .catch(err => {
        console.log('err:', err)
    });
//输出：  result: [1,2,3,4]
```

###### 手写Promise.race

谁先决议那么就返回谁，所以将all的计数器和逻辑判断全部去除掉就可以了。

一旦一个成功或者失败，则直接返回该promise。

```javascript
Promise.prototype.race = (iterators) => {
    let arr = Array.from(iterators);
	if (!Array.isArray (arr)) {
		throw new Error ("promises must be an array!!!");
	}
    return new Promise((resolve, reject) => {
        arr.forEach((item) => {
            Promise.resolve(item).then(value => {
                resolve(value);
            }).catch(err => {
                reject(err)
            })
        })
    });
}
```

###### 手写Promise.any

任何一个成功，则返回该promise。否则返回失败的promise

```javascript
Promise.prototype.any = (iterators) => {
    let arr = Array.from(iterators);
    let errs = [];
    let len = arr.length;
    return new Promise((resolve, reject) => {
        arr.forEach((item) => {
            Promise.resolve(item).then(value => {
                resolve(value);
            }).catch(err => {
                len--;
                errs.push(err);
                if(len === 0){
                  reject(errs);
                }
            })
        })
    });
}
```

[手写Promise.any](https://juejin.cn/post/6965596525388890142)

##### 8、then和catch混合使用

###### 题目一：

```javascript
var p1 = new Promise(function(resolve, reject) {
  resolve('Success!');
});

p1.then(function(value) {
  console.log(value); // "Success!"
  throw 'oh, no!';
}).catch(function(e) {
  console.log(e); // "oh, no!"
}).then(function(){
  // then接受两个参数，第一个是reslove的回调，第二个是reject的回调
  console.log('after a catch the chain is restored');
}, function () {
  console.log('Not fired due to the catch');
});

// 以下行为与上述相同
p1.then(function(value) {
  console.log(value); // "Success!"
  return Promise.reject('oh, no!');
}).catch(function(e) {
  console.log(e); // "oh, no!"
}).then(function(){
  console.log('after a catch the chain is restored');
}, function () {
  console.log('Not fired due to the catch');
});

/* 打印：
Success!
oh, no!
after a catch the chain is restored
*/
```

###### 题目二：

```javascript
// 抛出一个错误，大多数时候将调用catch方法
var p1 = new Promise(function(resolve, reject) {
  throw 'Uh-oh!';
});

p1.catch(function(e) {
  console.log(e); // "Uh-oh!"
});

// 在异步函数中抛出的错误不会被catch捕获到
var p2 = new Promise(function(resolve, reject) {
  setTimeout(function() {
    throw 'Uncaught Exception!';
  }, 1000);
});

p2.catch(function(e) {
  console.log(e); // 不会执行
});

// 在resolve()后面抛出的错误会被忽略
var p3 = new Promise(function(resolve, reject) {
  resolve();
  throw 'Silenced Exception!';
});

p3.catch(function(e) {
   console.log(e); // 不会执行
});
```

###### 题目三：

```javascript
//创建一个新的 Promise ，且已决议
var p1 = Promise.resolve("calling next");

var p2 = p1.catch(function (reason) {
    //这个方法永远不会调用
    console.log("catch p1!");
    console.log(reason);
});

p2.then(function (value) {
    console.log("next promise's onFulfilled"); /* next promise's onFulfilled */
    console.log(value); /* calling next */
}, function (reason) {
    console.log("next promise's onRejected");
    console.log(reason);
});

/*
next promise's onFulfilled
calling next
*/
```

`p.catch(onRejected);`：如果 `onRejected` 抛出一个错误或返回一个本身失败的 Promise ，  通过 `catch()` 返回的Promise 被rejected；**否则，它将显示为成功（resolved）。**

###### 题目四：

```javascript
// using a resolved promise, the 'then' block will be triggered instantly,
// but its handlers will be triggered asynchronously as demonstrated by the console.logs
const resolvedProm = Promise.resolve(33);

let thenProm = resolvedProm.then(value => {
    console.log("this gets called after the end of the main stack. the value received and returned is: " + value);
    return value;
});
// instantly logging the value of thenProm
console.log(thenProm);

// using setTimeout we can postpone the execution of a function to the moment the stack is empty
setTimeout(() => {
    console.log(thenProm);
});


// 上面的代码会依次返回：
// Promise {[[PromiseStatus]]: "pending", [[PromiseValue]]: undefined}
// "this gets called after the end of the main stack. the value received and returned is: 33"
// Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: 33}
```

全局打印thenProm的时候，为什么处于pending状态？

> 同步代码 -> 微任务 -> 宏任务

###### 题目五：

```javascript
Promise.resolve("foo")
  // 1. 接收 "foo" 并与 "bar" 拼接，并将其结果做为下一个 resolve 返回。
  .then(function(string) {
    return new Promise(function(resolve, reject) {
      setTimeout(function() {
        string += 'bar';
        resolve(string);
      }, 1);
    });
  })
  // 2. 接收 "foobar", 放入一个异步函数中处理该字符串
  // 并将其打印到控制台中, 但是不将处理后的字符串返回到下一个。
  .then(function(string) {
    setTimeout(function() { // 宏任务被放到下一个任务队列中
      string += 'baz';
      console.log(string);
    }, 1)
    return string;
  })
  // 3. 打印本节中代码将如何运行的帮助消息，
  // 字符串实际上是由上一个回调函数之前的那块异步代码处理的。
  .then(function(string) {
    console.log("Last Then:  oops... didn't bother to instantiate and return " +
                "a promise in the prior then so the sequence may be a bit " +
                "surprising");

    // 注意 `string` 这时不会存在 'baz'。
    // 因为这是发生在我们通过setTimeout模拟的异步函数中。
    console.log(string);
  });

// 打印:
// Last Then: oops... didn't bother to instantiate and return a promise in the prior then so the sequence may be a bit surprising
// foobar
// foobarbaz
```

###### 题目六：

```javascript
var p2 = new Promise(function(resolve, reject) {
  resolve(1);
});

p2.then(function(value) {
  console.log(value); // 1
  return value + 1;
}).then(function(value) {
  console.log(value + ' - A synchronous value works');
});

p2.then(function(value) {
  console.log(value); // 1
});

/*打印:
1
1
2 - A synchronous value works
*/
```

###### 题目七：

```javascript
Promise.reject()
  .then(() => 99, () => 42) // onRejected returns 42 which is wrapped in a resolving Promise
  .then(solution => console.log('Resolved with ' + solution)); // Resolved with 42

// 打印：Resolved with 42
```

###### 题目八：

```javascript
Promise.resolve()
  .then(() => {
    // 使 .then() 返回一个 rejected promise
    throw new Error('Oh no!');
  })
  .catch(error => {
    console.error('onRejected function called: ' + error.message);
  }) // 只要没有显式的reject，则都可以看成resolve
  .then(() => {
    console.log("I am always called even if the prior then's promise rejects");
  });

/*打印：
onRejected function called: Oh no!
I am always called even if the prior then's promise rejects
*/
```

catch后面的then还是会被执行。

###### 题目九：

```javascript
function resolveLater(resolve, reject) {
  setTimeout(function() {
    resolve(10);
  }, 1000);
}
function rejectLater(resolve, reject) {
  setTimeout(function() {
    reject(new Error('Error'));
  }, 1000);
}

var p1 = Promise.resolve('foo');
var p2 = p1.then(function() {
  // Return promise here, that will be resolved to 10 after 1 second
  return new Promise(resolveLater);
});
p2.then(function(v) {
  console.log('resolved', v);  // "resolved", 10
}, function(e) {
  // not called
  console.error('rejected', e);
});

var p3 = p1.then(function() {
  // Return promise here, that will be rejected with 'Error' after 1 second
  return new Promise(rejectLater);
});
p3.then(function(v) {
  // not called
  console.log('resolved', v);
}, function(e) {
  console.error('rejected', e); // "rejected", 'Error'
});
```

##### 9、根据promiseA+实现一个自己的promise

参考博客：[Promise不会？？看这里！！！史上最通俗易懂的Promise！！！](https://juejin.cn/post/6844903607968481287#heading-11)

```javascript
function resolvePromise(promise2,x,resolve,reject){
    //判断x是不是promise
    //规范中规定：我们允许别人乱写，这个代码可以实现我们的promise和别人的promise 进行交互
    if(promise2 === x){//不能自己等待自己完成
        return reject(new TypeError('循环引用'));
    };
    // x是除了null以外的对象或者函数
    if(x !=null && (typeof x === 'object' || typeof x === 'function')){
        let called;//防止成功后调用失败
        try{//防止取then是出现异常  object.defineProperty
            let then = x.then;//取x的then方法 {then:{}}
            if(typeof then === 'function'){//如果then是函数就认为他是promise
                //call第一个参数是this，后面的是成功的回调和失败的回调
                then.call(x,y => {//如果Y是promise就继续递归promise
                    if(called) return;
                    called = true;
                    resolvePromise(promise2,y,resolve,reject)
                },r => { //只要失败了就失败了
                    if(called) return;
                    called = true;
                    reject(r);  
                });
            }else{//then是一个普通对象，就直接成功即可
                resolve(x);
            }
        }catch (e){
            if(called) return;
            called = true;
            reject(e)
        }
    }else{//x = 123 x就是一个普通值 作为下个then成功的参数
        resolve(x)
    }

}

class Promise {
    constructor (executor){
        //默认状态是等待状态
        this.status = 'panding';
        this.value = undefined;
        this.reason = undefined;
        //存放成功的回调
        this.onResolvedCallbacks = [];
        //存放失败的回调
        this.onRejectedCallbacks = [];
        let resolve = (data) => {//this指的是实例
            if(this.status === 'pending'){
                this.value = data;
                this.status = "resolved";
                this.onResolvedCallbacks.forEach(fn => fn());
            }
 
        }
        let reject = (reason) => {
            if(this.status === 'pending'){
                this.reason = reason;
                this.status = 'rejected';
                this.onRejectedCallbacks.forEach(fn => fn());
            }
        }
        try{//执行时可能会发生异常
            executor(resolve,reject);
        }catch (e){
            reject(e);//promise失败了
        }
       
    }
    then(onFuiFilled,onRejected){ 
        //防止值得穿透 
        onFuiFilled = typeof onFuiFilled === 'function' ? onFuiFilled : y => y;
        onRejected = typeof onRejected === 'function' ? onRejected :err => {throw err;}        
        let promise2;//作为下一次then方法的promise
       if(this.status === 'resolved'){
           promise2 = new Promise((resolve,reject) => {
               setTimeout(() => {
                  try{
                        //成功的逻辑 失败的逻辑
                        let x = onFuiFilled(this.value);
                        //看x是不是promise 如果是promise取他的结果 作为promise2成功的的结果
                        //如果返回一个普通值，作为promise2成功的结果
                        //resolvePromise可以解析x和promise2之间的关系
                        //在resolvePromise中传入四个参数，第一个是返回的promise，第二个是返回的结果，第三个和第四个分别是resolve()和reject()的方法。
                        resolvePromise(promise2,x,resolve,reject)
                  }catch(e){
                        reject(e);
                  } 
               },0)
           }); 
       } 
       if(this.status === 'rejected'){
            promise2 = new Promise((resolve,reject) => {
                setTimeout(() => {
                    try{
                        let x = onRejected(this.reason);
                        //在resolvePromise中传入四个参数，第一个是返回的promise，第二个是返回的结果，第三个和第四个分别是resolve()和reject()的方法。
                        resolvePromise(promise2,x,resolve,reject)
                    }catch(e){
                        reject(e);
                    }
                },0)

            });
       }
       //当前既没有完成也没有失败
       if(this.status === 'pending'){
           promise2 = new Promise((resolve,reject) => {
               //把成功的函数一个个存放到成功回调函数数组中
                this.onResolvedCallbacks.push( () =>{
                    setTimeout(() => {
                        try{
                            let x = onFuiFilled(this.value);
                            resolvePromise(promise2,x,resolve,reject);
                        }catch(e){
                            reject(e);
                        }
                    },0)
                });
                //把失败的函数一个个存放到失败回调函数数组中
                this.onRejectedCallbacks.push( ()=>{
                    setTimeout(() => {
                        try{
                            let x = onRejected(this.reason);
                            resolvePromise(promise2,x,resolve,reject)
                        }catch(e){
                            reject(e)
                        }
                    },0)
                })
           })
       }
       return promise2;//调用then后返回一个新的promise
    }
    catch (onRejected) {
        // catch 方法就是then方法没有成功的简写
        return this.then(null, onRejected);
    }
}
Promise.all = function (promises) {
    //promises是一个promise的数组
    return new Promise(function (resolve, reject) {
        let arr = []; //arr是最终返回值的结果
        let i = 0; // 表示成功了多少次
        function processData(index, data) {
            arr[index] = data;
            if (++i === promises.length) {
                resolve(arr);
            }
        }
        for (let i = 0; i < promises.length; i++) {
            promises[i].then(function (data) {
                processData(i, data)
            }, reject)
        }
    })
}
// 只要有一个promise成功了 就算成功。如果第一个失败了就失败了
Promise.race = function (promises) {
    return new Promise((resolve, reject) => {
        for (var i = 0; i < promises.length; i++) {
            promises[i].then(resolve,reject)
        }
    })
}
// 生成一个成功的promise
Promise.resolve = function(value){
    return new Promise((resolve,reject) => resolve(value);
}
// 生成一个失败的promise
Promise.reject = function(reason){
    return new Promise((resolve,reject) => reject(reason));
}
Promise.defer = Promise.deferred = function () {
    let dfd = {};
    dfd.promise = new Promise( (resolve, reject) =>  {
        dfd.resolve = resolve;
        dfd.reject = reject;
    });
    return dfd
}
module.exports = Promise;
```



参考博客：[概念](https://juejin.cn/post/6844903488695042062)、[手写](https://juejin.cn/post/6844903617619558408)、[阮一峰ES6](https://es6.ruanyifeng.com/#docs/promise)、[【建议星星】要就来45道Promise面试题一次爽到底(1.1w字用心整理)](https://juejin.cn/post/6844904077537574919)