### console问题

#### 1、promise

##### 1.1 题目一

```javascript
const promise = new Promise((resolve, reject) => {
  console.log(1);
  setTimeout(() => {
    console.log("timerStart");
    resolve("success");
    console.log("timerEnd");
  }, 0);
  console.log(2);
});
promise.then((res) => {
  console.log(res);
});
console.log(4);

/*打印：
1
2
4
timerStart
timerEnd
success
*/
```

##### 1.2 题目二

```javascript
setTimeout(() => {
  console.log('timer1');
  setTimeout(() => {
    console.log('timer3')
  }, 0)
}, 0)
setTimeout(() => {
  console.log('timer2')
}, 0)
console.log('start')

/*打印：
start
timer1
timer2
timer3
*/
```

##### 1.3 题目三

```javascript
setTimeout(() => {
  console.log('timer1');
  Promise.resolve().then(() => {
    console.log('promise')
  })
}, 0)
setTimeout(() => {
  console.log('timer2')
}, 0)
console.log('start')

/*打印：
start
timer1
promise
timer2
*/
```

- 每一轮宏任务后紧跟微任务。

##### 1.4 题目四

```javascript
setImmediate(function(){
    console.log(1);
}, 0)

console.log(2);

setTimeout(function(){
    console.log(3);
}, 0)

new Promise(function(resolve){
    console.log(4);
    resolve();
    console.log(5);
}).then(function(){
    console.log(6);
})

console.log(7);

process.nextTick(function(){
    console.log(8);
})

console.log(9);

/*打印：
2
4
5
7
9
8
6
3
1
*/
```

- 优先级：process.nextTick() > promise.then() > setTimeout > setImmediate

##### 1.5 题目五

```javascript
async function async1(){
    console.log('1');
    await async2();
    console.log('2'); // 被放到微任务Promise.then中，当当前宏任务执行完毕再来执行微任务
}
async function async2(){
    console.log('3');
}
console.log('4');
setTimeout(function(){ // 下一轮宏任务
    console.log('5');
})
async1();
new Promise((resolve)=>{
    console.log('6');
    resolve()
}).then(()=>{
    console.log('7');
})
console.log('8');

/*打印：
4
1
3
6
8
2
7
5
*/
```

- 紧跟着await后面的语句相当于放到了new Promise中，下一行及之后的语句相当于放在Promise.then中 。

##### 1.6 题目六

```javascript
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
  setTimeout(() => {
    console.log('timer1') // 被放到当前微任务的下下一轮宏任务中，所以是最后执行
  }, 0)
}
async function async2() {
  setTimeout(() => {
    console.log('timer2')
  }, 0)
  console.log("async2");
}
async1();
setTimeout(() => {
  console.log('timer3')
}, 0)
console.log("start")

/*打印：
'async1 start'
'async2'
'start'
'async1 end'
'timer2'
'timer3'
'timer1'
*/
```

- 定时器谁先执行，你只需要关注谁先被调用的以及延迟时间是多少，这道题中延迟时间都是`0`，所以只要关注谁先被调用的。

##### 1.7 题目七

```javascript
async function async1 () {
  console.log('async1 start');
  await new Promise(resolve => {
    console.log('promise1')
  })
  console.log('async1 success');
  return 'async1 end'
}
console.log('srcipt start')
async1().then(res => console.log(res))
console.log('srcipt end')

/*打印：
srcipt start
async1 start
promise1
srcipt end
*/
```

- await相当于promise.then，题目中的await后面的promise没有返回值，相当于它的状态一直是pending状态，所以await后面的代码不会执行，以及async1().then也不会执行。

await后面跟一个promise对象：会等待该promise resolve或者reject

await后面跟一个数字：直接转换成resolve，相当于await Promise.resolve(4)

await后面跟一个async函数：会转换成resolve的promise

区别于：

```javascript
async function async1 () {
  console.log('async1 start');
  await new Promise(resolve => {
    console.log('promise1')
    resolve('promise1 resolve') // 后面代码为微任务
  }).then(res => console.log(res))
  console.log('async1 success');
  return 'async1 end'
}
console.log('srcipt start')
async1().then(res => console.log(res)) // 微任务
console.log('srcipt end')

/*打印：
srcipt start
async1 start
promise1
srcipt end
promise1 resolve
async1 success
async1 end
*/
```

区别于：

```javascript
async function async1 () {
  console.log('async1 start');
  await new Promise(resolve => {
    console.log('promise1')
    resolve('promise resolve') // 处于resolved阶段，但没有then来接受，所以不会打印
  })
  console.log('async1 success');
  return 'async1 end'
}
console.log('srcipt start')
async1().then(res => {
  console.log(res)
})
new Promise(resolve => {
  console.log('promise2')
  setTimeout(() => {
    console.log('timer')
  })
})

/*打印：
srcipt start
async1 start
promise1
promise2
async1 success
async1 end
timer
*/
```

##### 1.8 题目八

```javascript
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}

async function async2() {
  console.log("async2");
}

console.log("script start");

setTimeout(function() {
  console.log("setTimeout");
}, 0);

async1();

new Promise(function(resolve) {
  console.log("promise1");
  resolve();
}).then(function() {
  console.log("promise2");
});
console.log('script end')

/*打印：
srcipt start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
*/
```

##### 1.9 题目九

```javascript
const promise = new Promise((resolve, reject) => {
  reject("error");
  resolve("success2");
});
promise
.then(res => {
    console.log("then1: ", res);
  }).then(res => {
    console.log("then2: ", res);
  }).catch(err => {
    console.log("catch: ", err);
  }).then(res => {
    console.log("then3: ", res);
  })

// 打印：
// catch: error
// then3: undefined
```

- `catch`不管被连接到哪里，都能捕获上层未捕捉过的错误。

- 至于`then3`也会被执行，那是因为`catch()`也会返回一个`Promise`，且由于这个`Promise`没有返回值，所以打印出来的是`undefined`。

##### 1.10 题目十

```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('timer')
    resolve('success')
  }, 1000)
})
const start = Date.now();
promise.then(res => {
  console.log(res, Date.now() - start)
})
promise.then(res => {
  console.log(res, Date.now() - start)
})

// 打印：
// timer
// success 1001
// success 1002
```

- 当然，如果你足够快的话，也可能两个都是`1001`。

- `Promise` 的 `.then` 或者 `.catch` 可以被调用多次，但这里 `Promise` 构造函数只执行一次。或者说 `promise` 内部状态一经改变，并且有了一个值，那么后续每次调用 `.then` 或者 `.catch` 都会直接拿到该值。

##### 1.11 题目十一

```javascript
Promise.resolve().then(() => {
  return new Error('error!!!')
}).then(res => {
  console.log("then: ", res)
}).catch(err => {
  console.log("catch: ", err)
})

// 打印：
// then: Error: error!!!
```

返回任意一个非 `promise` 的值都会被包裹成 `promise` 对象，因此这里的`return new Error('error!!!')`也被包裹成了`return Promise.resolve(new Error('error!!!'))`。

当然如果你抛出一个错误的话，可以用下面👇两的任意一种：

```javascript
return Promise.reject(new Error('error!!!'));
// or
throw new Error('error!!!')
```

##### 1.12 题目十二

```javascript
Promise.resolve(1)
  .then(2) // 发生值透传
  .then(Promise.resolve(3)) // 发生值透传
  .then(console.log)

// 打印：1
```

- `.then` 或者 `.catch` 的参数期望是函数，传入非函数则会发生**值透传**。

- 第一个`then`和第二个`then`中传入的都不是函数，一个是数字类型，一个是对象类型，因此发生了透传，将`resolve(1)` 的值直接传到最后一个`then`里。

##### 1.13 题目十三

```javascript
Promise.resolve()
  .then(function success (res) {
    throw new Error('error!!!')
  }, function fail1 (err) {
    console.log('fail1', err)
  }).catch(function fail2 (err) {
    console.log('fail2', err)
  })

// 打印：fail2 Error: error!!!
```

-  由于`Promise`调用的是`resolve()`，因此`.then()`执行的应该是`success()`函数，可是`success()`函数抛出的是一个错误，它会被后面的`catch()`给捕获到，而不是被`fail1`函数捕获。 

##### 1.14 题目十四

```javascript
function runAsync (x) {
  const p = new Promise(r => setTimeout(() => r(x, console.log(x)), 1000))
  return p
}
function runReject (x) {
  const p = new Promise((res, rej) => setTimeout(() => rej(`Error: ${x}`, console.log(x)), 1000 * x))
  return p
}
Promise.all([runAsync(1), runReject(4), runAsync(3), runReject(2)])
  .then(res => console.log(res))
  .catch(err => console.log(err))

// 打印：
// 1s后输出
1
3
// 2s后输出
2
Error: 2
// 4s后输出
4
```

- r(x, console.log(x))这种写法，会在reslove的时候立即执行console.log。

```js
// 这种写法才是1s后console
setTimeout(()=>{
  console.log(x);
  resolve(x);
},1000)
```



- 并行执行多个异步操作，并且在一个回调中处理所有的返回数据。

>  有一个场景是很适合用这个的，一些游戏类的素材比较多的应用，打开网页时，预先加载需要用到的各种资源如图片、flash以及各种静态文件。所有的都加载完后，我们再进行页面的初始化。 

区别于race：

```javascript
function runAsync(x) {
  const p = new Promise(r =>
    setTimeout(() => r(x, console.log(x)), 1000)
  );
  return p;
}
function runReject(x) {
  const p = new Promise((res, rej) =>
    setTimeout(() => rej(`Error: ${x}`, console.log(x)), 1000 * x)
  );
  return p;
}
Promise.race([runReject(0), runAsync(1), runAsync(2), runAsync(3)])
  .then(res => console.log("result: ", res))
  .catch(err => console.log(err));

/* 打印：
0
'Error: 0'
1
2
3
*/
```

- `runReject(0)`最先执行完，所以进入了`catch()`中。

总结：

- `Promise.all()`的作用是接收一组异步任务，然后**并行执行异步任务**，并且在所有异步操作执行完后才执行回调。

- `race()`的作用也是接收一组异步任务，然后**并行执行异步任务**，只保留取第一个执行完成的异步操作的结果并执行回调，其他的方法仍在执行，不过执行结果会被抛弃。

- `Promise.all().then()`结果中数组的顺序和`Promise.all()`接收到的数组顺序一致。

- `all和race`传入的数组中如果有会抛出异常的异步任务，那么只有最先抛出的错误会被捕获，并且是被`then`的第二个参数或者后面的`catch`捕获；但并不会影响数组中其它的异步任务的执行。

题目来源：[【建议星星】要就来45道Promise面试题一次爽到底(1.1w字用心整理)](https://juejin.cn/post/6844904077537574919#heading-0)

#### 2、闭包

##### 2.1 题目一

```javascript
var fn = null;
var foo = function(){
    var a = 1;
    function innerFoo(){
        console.log(a);
        console.log(b);
    }
    fn = innerFoo; // 将innerFoo赋值给全局变量fn，形成闭包，一直存在于内存当中
}
var bar = function(){
    var b = 2;
    fn(); // 即调用闭包innerFoo()，闭包函数里面的a，b从闭包的作用域中查找
}
foo() // 给fn赋值
bar() // 调用fn

/*打印：
1
b is not undefined
*/
```

##### 2.2 题目二

```javascript
function fun(){
    var printout = function(i){
        setTimeout(function(){
            console.log(new Date, i)
        },1000)
    };

    for(let i = 0; i<5; i++){
        printout(i)
    }

    console.log(new Date,i)
}

fun()

/* 打印：
i is not undefined          // 如果上面的for循环let改为var，则打印5
0 
1 
2 
3  
4
*/
```

##### 2.3 题目三

```javascript
function fun(){
    var arr = []
    for(var i = 1; i<5; i++){
        (function(i){
            setTimeout(function(){
                arr.push(i)
            },0)
        })(i)
    }
    arr.push(0)
    return arr
}

console.log(fun())

/* 打印：
0 1 2 3 4
*/
```

##### 2.4 题目四

```javascript
for(var i = 0; i<5; i++){
    setTimeout(function(){
        console.log(i);
    },1000)
}

/* 打印：
5
5
5
5
5
*/
```

##### 2.5 题目五

```javascript
var fn = null;
function foo() {
    var a = 2;
    function innnerFoo() {
        console.log(c); // 在这里，试图访问函数bar中的c变量，会抛出错误，打印Uncaught ReferenceError: c is not defined，后面的a不会被执行
        console.log(a);
    }
    fn = innnerFoo; // 将 innnerFoo的引用，赋值给全局变量中的fn
}

function bar() {
    var c = 100;
    fn(); // 此处的保留的innerFoo的引用
}

foo();
bar();
```

##### 2.6 题目六

```javascript
(function () {
    var a = 1;
    var b = 2;

    function add(num1, num2) {
        var num1 = !!num1 ? num1 : a;
        var num2 = !!num2 ? num2 : b;

        return num1 + num2;
    }

    window.add = add;
})();

console.log(add()); // 3
console.log(add(10, 20)); // 30
```

模块化：使用函数自执行的方式，创建了一个模块。add是模块对外暴露的一个公共方法。而变量a，b被作为私有变量。

#### 3、this指向

##### 3.1 题目一

```javascript
var  o1 = {
    name:'o1',
    fn:function(){
        return  this.name
    }
}

var  o2 = {
    name:'o2',
    fn:function(){
        return o1.fn() // 调用o2.fn()，实际上是调用o1.fn()
    }
}

var  o3 = {
    name:'o3',
    fn:function(){
        // fn保存o1.fn的引用，导致o1的this丢失
        let fn = o1.fn; // 当函数作为普通函数调用时，this在非严格模式下指向全局对象（浏览器中是window）
        return fn()
    }
}

var name = 'oo'

console.log(o1.fn())
console.log(o2.fn())
console.log(o3.fn())

/*打印：
o1
o1
oo
*/
```

##### 3.2 题目二

函数作为**构造函数**被调用（使用new），会初始化一个实例对象，函数内部的this指向这个实例对象。

函数作为**普通函数**被调用，函数内部的this指向全局对象（windows）。

```javascript
abc = 1;
function Foo(){
    this.abc = 2; // 表示每个 Foo 实例会拥有自己的属性 abc，值为 2
}
var foo = new Foo(); // Foo函数作为构造函数被调用
alert(foo.abc);

// 打印：2

// 区别于下面的写法：
abc = 1;
function Foo(){
    abc=2; // 修改全局变量
    console.log(this.abc); // 2
}
Foo(); // 这里是直接调用函数，函数里面的this会指向全局变量
console.log(abc) // 2
```

##### 3.3 题目三

```javascript
var ctx = {_val: 30};
var _val = 10;
function rootFn(){
    console.log(this._val);
    this._val = 20;
    function childFn(){
        console.log(this._val);
    }
    return childFn; // 闭包
}
rootFn.apply(ctx)(); // rootFn作用域的this指向ctx对象，第一个()执行rootFn，第二个()执行childFn。childFn是作为普通函数调用的，没有显式的this绑定，所以this指向全局对象
console.log(ctx._val);

// 打印：
// 30 
// 10 
// 20
```

闭包的链式作用域

childFn是**作为普通函数调用的，没有显式的this绑定，所以this指向全局对象**

##### 3.4 题目四

```javascript
const o = {
name: 'didi',
getName() {
    return function() {
        console.log(this.name);
    }
}
}
const func = o.getName();
func() // undefined，作为普通函数被调用，此时this指向全局对象（全局对象没有name）

// 或者使用bind、call改变this指向
func.bind(o)()
// 或者func.call(o) // 立即执行

// 要使输出didi，可以使用箭头函数
const o = {
name: 'didi',
getName() {
    return () => {
        console.log(this.name); // 箭头函数没有自己的this，指向外部上下文的this
    }
}
}
const func = o.getName();
func()

// 区别
const o = {
name: 'didi',
getName:() => {
    return () => {
        console.log(this.name); // 这里的this指向全局对象，此时没有全局属性name，所以打印空的字符串 ''
    }
}
}
// var name='test' // 如果加了这一行的话会打印test
const func = o.getName();
func()
```

注意一下这里的区别：

如果是普通函数，这里的this会指向全局对象

如果是箭头函数，这里的this直接继承外层的对象o的this，无论后续怎么调用，永远为o.getName的上下文

#### 4、作用域

##### 4.1 题目一 ？？？？？

**函数声明的优先级最高**，它永远被提升至作用域最顶部，然后才是**函数表达式和变量按顺序执行**。

具名函数表达式中的函数名只在函数内部有效，并且这个标识符是常量，不允许修改

```javascript
var b = 10;
// 具名函数表达式中的函数名只在函数内部有效，并且这个标识符是常量，不允许修改
(function b(){
    b = 20;
    console.log(b);
})();

/*打印：
function b(){}
*/


// 区别：
var b = 10;
function b(){
    b = 20;
    console.log(b);
};
b() // 这里的b表示10,并不是函数b

// 区别：
var b = 10;
// 匿名函数
(function() { 
    b = 20; // 修改全局变量 b
    console.log(b); // 输出 20
})();
```

##### 4.2 题目二

函数声明提升

```javascript
function a(x){
    return x*2;
}
var a;
console.log(typeof(a));

// 打印：function

// 区别：
function a(x){
    return x*2;
}
var a=10; // 为a赋值
console.log(typeof(a)); // number
```

##### 4.3 题目三

```javascript
var a = 1;
function foo(){
    console.log(a);
    var a = 2;
}
foo()
console.log(a) // 1

// 打印：undefined
```

##### 4.4 题目四

```javascript
let x = 1;
function add(x){
    return (x = x + 1);
}
let y = add(x);

function add(x){
    return (x = x + 3);
}
let z = add(x);

console.log(y + ',' + z);

// 打印：4,4
```

- 函数声明优先级最高，会被提升至作用域顶部。
- add()中的形参x属于局部作用域，与全局作用域下的x是独立的；如果没有声明形参x的话，才会影响到全局下的x。
-  任何情况下，**离得最近的变量**绑定优先级最高 。

##### 4.5 题目五

```javascript
var a = 10, b = 11, c = 12;
function test(a){
    a = 1; // 形参a
    var b = 2; // 函数内部的b
    c = 3;
}
test(10)
console.log(a) // 10
console.log(b) // 11
console.log(c) // 3
```

- 变量a，b在test()函数的作用域里面；c因为在test()里面没有被定义，所以引用的是全局作用域的c。

##### 4.6 题目六

```javascript
function f(){}
console.log(typeof f.prototype)
console.log(typeof Object.prototype)
console.log(typeof Function.prototype.prototype)
console.log(typeof f.prototype.constructor)

// 打印：object object undefined function
```

##### 4.7 题目七

```javascript
var f = NaN;
function f(){}
if (typeof f === 'number') {
    console.log(100);
} else if (typeof f === 'function'){
    console.log(200);
} else if (typeof f === 'object'){
    console.log(300);
} else {
    console.log(400);
}

// 打印：100
```

- 预编译：变量提升；NaN本质是一个number

##### 4.8 题目八

```javascript
let obj = {
    a: 100,
    log(){
        a = 200; // 隐式的创建了一个全局变量
        console.log(this.a);
    }
}
obj.log();
let { log } = obj;
log(); // this指向全局变量
console.log(a)

// 打印：100 200 200
```

##### 4.9 题目九

`var` 声明的变量是 **函数作用域** 或 **全局作用域**，不受块级作用域（`{}`）限制

`let` 声明的变量是 **块级作用域**，仅在当前代码块 `{}` 内有效。

```javascript
{var a = 0}; // 全局
{let b = 0};
{let a = 1};
{var b = 1}; // 全局
console.log(a,b) // 0 1
```

##### 4.10 题目十

const、let 声明的变量不会成为全局对象的属性。只有var声明的变量才可以作为全局对象的属性。

```javascript
const a=1 // const 声明的变量不会成为全局对象的属性
// 如果这里改成var a=1, 则会输出 2 1 1 1
const obj={
    a:2,
    show1:function(){
        console.log(this.a)
    },
    show2:()=>{
        console.log(this.a)
    },
}

obj.show1()
obj.show2()

const func1=obj.show1
const func2=obj.show2
func1()
func2()

// 打印：2 undefined undefined undefined 
```



#### 5、原型链

##### 5.1 题目一

```javascript
function fn1(){}
function fn2(){}
fn1.prototype = new fn2(); // fn1.prototype._proto_ = fn2.prototype
fn2.prototype = new fn1(); // fn2.prototype._proto_ = fn1.prototype
const f1 = new fn1(); // f1._proto_ = fn1.prototype，fn1.prototype._proto_ = fn2.prototype
const f2 = new fn2(); // f2._proto_ = fn2.prototype，fn2.prototype._proto_ = fn1.prototype
console.log(f1.constructor, f2.constructor) // fn2 fn2
// f1.constructor和f1._proto_.constructor是一样的，都是获取构造函数
```

```
f1 的原型链:
f1 → fn1.prototype（fn2实例） → fn2.prototype（原始） → Object.prototype → null
                                 ↑
f2 的原型链:
f2 → fn2.prototype（fn1实例） → fn1.prototype（fn2实例） → fn2.prototype（原始） → Object.prototype → null
```

##### 5.2 题目二

```javascript
let base = {
    name: 'base'
};
let A = function (){
    this.name = 'a';
}
A.prototype = Object.create(base); // A.prototype.__proto__ === base（关键点）
let a = new A();
base.name = 'new_name';
console.log(a.name); // a

delete a.name; // a.name被删除后，会继续在原型链上面查找
console.log(a.name); // new_name
```

- `Object.create(proto[, propertiesObject])`：使用指定的**原型对象**及其属性去创建一个新的对象。

##### 5.3 题目三

```javascript
Function.prototype.f = function(){
    return Function.prototype.call.bind(this);
}
console.log(Array.prototype.push.f()([],0,1,2)); // prototype is not defined
```

##### 5.4 题目四

```javascript
function f(){}
f.prototype.name = 100; // 原型上添加name属性
let a = new f();
console.log(a.name); // 100

f.prototype.name = 200;
console.log(a.name); // 200

f.prototype = { name: 300 };
console.log(a.name); // 200
```

向原型中添加方法：

- `f.prototype.xxx = function(){}`
- `f.prototype = {}`，需要显示指定`f.prototype.constructor = f`

例子：

```javascript
    function User(name) {
        this.name = name
    }
 
    User.prototype = {
        constructor: User,
        show() {
            console.log(this.name)
        }
    };
    let Joker = new User.prototype.constructor("Joker");
    Joker.show(); // Joker 
```



#### 6、其他

##### 6.1 typeof

(1)

```javascript
typeof function(){} // function
typeof null // object
```

(2)

```javascript
function f(name){
    return this.name = name;
}
console.log([0,'0',[],{},null].map(i => typeof new f(i).name));

// 打印： [ "number", "string", "undefined", "undefined", "object" ]
```

##### 6.2 ||和&&

```javascript
var a = 'string';
var b = 0;
var c = true;
console.log(b || a);
console.log(b && a);
console.log(c || b);
console.log(c && b);

// 打印： 'string', 0, true, 0
```

##### 6.3 数组遍历

(1)

```javascript
var arr = [0,0,0]
arr.forEach(val => val=1)
console.log('位置1', arr) // [0,0,0]

for(var i of arr){i=1}
console.log('位置2', arr) // [0,0,0]

arr.map(val => val=1)
console.log('位置3', arr) // [0,0,0]

for(var i in arr){arr[i]=1}
console.log('位置4', arr) // [1,1,1]
```

- for in遍历的是数组的下标；for of遍历的是value。
- `map((value,index,array)=>{})`返回一个新数组；forEach 返回值是undefined，不可以链式调用 ，没有办法终止或者跳出forEach()循环，除非抛出异常 。
- for循环通过**下标**改变原数组；`forEach((value,index,array)=>{})`没有返回值，对原数组无影响，但也可以通过下标改变；`for of`遍历的是value，不改变原数组。
- 看原数组是否发生改变，看通过**下标**还是value！！！

(2)

```javascript
let arr = ['a','b'];
for(let key in arr){
    console.log(key); // 0 1
}
for(let key of arr){
    console.log(key); // a b
}
```

(3)

```javascript
["1", "2", "3"].map(parseInt);  //结果[1, NaN, NaN]，等价于[parseInt(1,0),parseInt(2,1),parseInt(3,2)]　　　
// parseInt(1,0)//radix为0，1十进制为1
// parseInt(2,1)//radix小于2，返回NaN
// parseInt(3,2)//radix为2,3的二进制NaN

// 改为：
["1", "2", "3"].map(item => parseInt(item));　
// 原因：parseInt(string, radix)默认有两个参数，第二个参数是进制数。
```

`parseInt(string, radix)`：

- 如果**省略该参数**或其值为 **0**，则数字将以 10 为基础来解析。 
- 如果其值为2，以二进制解析。
- 如果该参数小于 2 或者大于 36，则 parseInt() 将返回 NaN。 
- 如果它以 “0x” 或 “0X” 开头，将以 16 为基数。 

##### 6.4 valueOf()和toString()

```javascript
console.log(![]) // false

console.log([]==false) // true
// 原因：==强制类型转换，右边false先变为0，左边[] -> [].valueOf()返回[] -> [].toString()返回'' -> 空字符串转换为0
// 原型对象都含有valueOf()和toString()方法

```

-  toString（）方法返回一个表示改对象的字符串 
-  valueOf（）方法返回指定对象的原始值

```javascript
[].valueOf() === [] // true
[].toString() ==='' // true

var obj={};
console.log(obj.toString()); // [object Object]
console.log(obj.valueOf()); // {}
```

##### 6.5 二进制浮点数

```javascript
const one = 0.1
const two = 0.2
const three = 0.3
console.log([two-one == one, three-two == one])

// 打印： [true, false]
```

下面判断语句正确的是：（ABCD）。

A：`parseFloat('1234blue') === 1234`

B：`parseFloat('22.34.5.22') === 22.34`

C：`parseFloat('0xA') === 0`

D：`parseFloat('0908.50') === 908.5`

##### 6.6 isNaN

下面返回结果为true的有（BC）。

A：`isNaN('10')`

B：`isNaN('blue')`

C：`isNaN(NaN)`

D：`isNaN(10)`

```javascript
NaN == NaN;       // false
NaN === NaN;      // false

isNaN(null);         // false
isNaN(undefined);    // true    Number(undefined) // NaN
isNaN({});           // true
isNaN([]);           // false
isNaN('');           // false
isNaN(1);            // false
isNaN('123');        // false
isNaN('0xa');        // false
```

- 在调用 isNaN 时，如果传入的参数不是 number 类型，则会尝试隐式转换成 number 类型的数据，所以，` isNaN(xxx) `其实与 `isNaN(Nunber(xxx))` 等价 

```javascript
null == undefined // true
[] == [] // false
null == 0 // false
undefined == '0' // false
NaN == false // false
[] == false // true
null === null // true
undefined === undefined // true
NaN === NaN // false
Symbol.for('a') === Symbol.for('a') // true
```

##### 6.7 delete

```javascript
var abc = 1234
abcd = 2345 // 通过未声明的变量赋值生成全局对象的属性，没有DontDelete

delete abc // false
console.log(abc)

delete abcd // true
console.log(abcd)

// 打印： 1234 ,  abcd is not defined
```

https://www.cnblogs.com/aaronjs/articles/3148934.html

- delete 只有当一个**属性无法被删除**时才返回 false 。

- 当声明变量和函数时，它们成为了变量对象（Variable object）——要么是活化对象（在函数代码中）， 要么是全局对象（在全局代码中）——的属性，这些属性**伴随生成了内部属性 DontDelete**。 然而，**任何显式/隐式赋值**的属性**不生成 DontDelete**。 

```javascript
// 例1
var GLOBAL_OBJECT = this;

/* 'foo'是全局对象的一个属性，
    它通过变量声明而生成，因此拥有内部属性DontDelete
    这就是为什么它不能被删除*/
var foo = 1;
delete foo; // false
typeof foo; // "number"

/* 'bar'是全局对象的一个属性，
    它通过变量声明而生成，因此拥有DontDelete子
    这就是为什么它同样不能被删除*/
function bar() {};
delete bar; // false
typeof bar; // "function"

/* 'baz'也是全局对象的一个属性，
    然而，它通过属性赋值而生成，因此没有DontDelete
    这就是为什么它可以被删除*/
GLOBAL_OBJECT.baz = "baz";
delete GLOBAL_OBJECT.baz; // true
typeof GLOBAL_OBJECT.baz; // "undefined"

// 例2
(function(foo,bar) {
    //不能删除'arguments'，因为有DontDelete
    delete arguments; // false;
    typeof arguments; // "object"
    delete foo; // false
    foo; // 1

    //也不能删除函数的length,因为有DontDelete
    function f() {};
    delete f.length; // false;
    typeof f.length; // "number"
})(1,"bah");

// 例3
// 删除对象的属性
var x = { a: 1 };
delete x.a; // true
x.a; // undefined
// 不能删除定义的变量
var x = 1;
delete x; // false;
x; // 1
// 不能删除函数
function x() {};
delete x; // false;
typeof x; // "function"
```

##### 6.8 node

(1)

```javascript
// b.js
exports = {
    value: 0
};
module.exports = {
    value: 1
};
exports.year = 2020;
module.exports.name = 'cvte';

// a,js
const {name, value, year} = require('./b.js');
console.log(name, value, year)

// 打印：cvte 1 2020
```

(2)

```javascript
setImmediate(()=>{
    new Promise(resolve => {
        setImmediate(()=>console.log(3));
        console.log(4);
        resolve();
    });
    process.nextTick(()=>{
         console.log(2);
    })
    setTimeout(()=>{
        console.log(1);
        setImmediate(()=>{
            console.log(5);
        })
    }, 0)
})
```

##### 6.9 String对象

```javascript
function showCase(value){
    switch(value){
        case 'A':
            console.log('A');
            break;
        case 'B':
            console.log('B');
            break;
        case undefined:
            console.log('undefined');
            break;
        default:
            console.log('do not know');
            break;
    }
}
showCase(new String('A'))

// 打印：do not know
```

##### 6.10 set()和get()？？？？？

(1)

```javascript
let x = {
    a: 100
};
Object.defineProperty(x,'a',{
    get(){
        return 200;
    }
});
x.a = 300; // set方法
console.log(x.a) // 200
```

(2)???????

```javascript
let x = {
    toString(){
        return 20;
    }
    valueOf(){
        return '30';
    }
};
console.log(x=='20');
console.log(x==30);
```

(3)

```javascript
let o = {
    get:f(){
        console.log('get');
    }
    set:f(a){
        console.log('set');
    }
}
o.f = '100';
console.log(o.f)
```

##### 6.11 形参

```javascript
function f(a=1){
    console.log(a);
}
f(); // 1
f(undefined); // 1
f(null); // null
f(false); // false
```

