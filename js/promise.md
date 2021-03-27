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



### Promise的三种状态

pending，resolved，rejected

- 当Promise刚创建完成时，处于pending状态；
- 当Promise中的函数参数执行了resolve后，Promise由pending状态变成resolved状态；
- 如果在Promise的函数参数中执行的不是resolve方法，而是reject方法，那么Promise会由pending状态变成rejected状态。



### 常见面试题

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

>

6、常与setTimeout结合考宏任务、微任务执行顺序：promise接收的函数参数是同步执行的，但then方法中的回调函数执行则是异步的。

7、为什么promise中的执行器函数是同步执行，而then()是异步执行？

> Promise是异步动作的同步处理方案。



参考博客：[概念](https://juejin.cn/post/6844903488695042062)、[手写](https://juejin.cn/post/6844903617619558408)、[阮一峰ES6](https://es6.ruanyifeng.com/#docs/promise)