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

### 迭代器

遍历与迭代的区别：从目标源依次逐个抽取的方式来提取数据。（目标源必须是有序的且连续的）

### async、await、promise三者关系

1、执行async函数，返回的都是promise对象

```javascript
async function test1(){
    return 1; // 被封装成promise对象再返回
}
async function test2(){
    return Promise.resolve(2);
}

const result1 = test1();
const result2 = test2();
console.log('result1', result1);
console.log('result2', result2);
```

![1626592675375](C:\Users\lhq\AppData\Roaming\Typora\typora-user-images\1626592675375.png)

2、promise.then成功的情况对应await

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
// await后面跟一个数字
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

3、promise.catch异常的情况对应try...catch

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

test6()
```

![1626594661029](C:\Users\lhq\AppData\Roaming\Typora\typora-user-images\1626594661029.png)

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