### 正式批 shopee虾皮1面

1、vue依赖收集机制

2、快排

3、二叉树中序遍历非递归方法

4、http报文组成

5、https，加密过程

6、tcp和udp区别

7、建立tcp连接的时候会存在什么问题，回答的阻塞

8、http2.0，头部阻塞

9、快速找出两个数组不同值，比如输入[1,2,3]和[2,3,4]，输出[1,4]

> 反馈：说话不自信，拖泥带水，不干脆，即使遇到不会的编程题之类的也要从容面对

### 补录 shopee虾皮1面

1、驼峰命名

> HelloWorld
>
> hello_world
>
> HELLO_WORLD
>
> 输出：helloWorld

考虑：正则、数组方法（分两种情况处理）

```javascript
function fun(s){
  let reg = /\_|[A-Z]/g;
  console.log(s.split(reg))
}
fun('HELLO_WORLD')
```

2、函数柯里化

> 实现curring(fn, length, ...args)函数
>
> ```javascript
> function add(a,b,c){
>   return a+b+c;
> }
> 
> let sum1 = curring(add,3,10)
> sum1(1)(2) // 13
> sum1(1,2) // 13
> sum1(1) // 不打印
> 
> let sum2 = curring(add)
> sum2(1,2,3) // 6
> sum2(1)(2) // 不打印
> ```

3、看输出

```javascript
let obj1 = {a:1}
let obj2 = {b:2}
let arr1 = [obj1];
let arr2 = [obj2];

let v1 = arr1.concat(arr2) // 返回一个新的数组
let v2 = arr1.concat(...arr2)
let v3 = arr1.push(arr2) // 返回新数组的长度
// console.log('v3:', arr1)
let v4 = arr1.push(...arr2)
// console.log('v4:', arr1)

console.log(v1) // [{a:1}, {b:2}]
console.log(v2) // [{a:1}, {b:2}]
console.log(v3) // 2
console.log(v4) // 3
```

4、css水平垂直居中

5、性能优化

6、csrf和xss攻击，还了解其它安全问题吗

7、vue的响应式原理，数组为什么不能下标访问

8、v-model可以自定义组件吗

9、js基本数据类型

### 补录 shopee虾皮2面

1、看代码说输出

```js
async function async1() {

    console.log(1);
    var p = Promise.resolve()

    p.then(function () {
        Promise.resolve().then(() => console.log(2)).then(() => console.log(3))
        console.log(4);
    }).then(v => console.log(5)).then(v => console.log(6));
    p.then(v => console.log(7))

    await new Promise(function (resolve) {
        console.log(8);
        resolve();
    })
}

async1();
console.log(9)

// 打印如下：1 8 9 4 7 2 5 3 6
```



```js
async function async1() {
    console.log(1);
    // await 会阻塞后续代码的执行
    await new Promise(function (resolve) {
        resolve();
        console.log(2);
    }).then(function () { // 压入微任务队列
        console.log(3);
    }); 
    console.log(4);
}

console.log(5);

setTimeout(function () {
    console.log(6);
}, 0);

async1();

console.log(7);

// 打印：5 1 2 7 3 4 6
```



2、csrf、xss攻击

3、https加密过程，http2的缺点 --- 引申出http3

4、有写过测试吗，单元测试

5、后端了解吗

6、typescript

7、项目优化、难点

9、算法：最长无重复子串

10、有没有优点，没有问到的东西：业务、ui等等，不一定技术

11、英语要求：设计文档要用英语写

12、反思

> 没有优点
>
> 不自信
>
> 问的都很有深度广度
>
> 回答问题太局限
>
> 多做项目

