### 定义
`this`指的是函数运行时所在的环境。

### 运行环境

```javascript
var f = function () {
  console.log(this.x); // 非严格模式下，独立函数调用的this指向全局对象（window）
}

var x = 1;
var obj = {
  f: f, // 把上面的函数f()赋值给属性f；在内存中，f属性存放的是函数f()的地址，所以调用的是f()的引用地址
  x: 2,
};

// 全局环境执行，this指向全局变量，即 this.x 指向全局环境的x
f() // 1

// obj 环境执行，this.x指向obj.x
obj.f() // 2
```

示意图：
- 全局环境中

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210305133602704.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
- obj环境中

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210305133613510.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
### this的四种用法
#### 第一种：纯粹的函数调用
这是函数的最通常用法，属于 **全局性调用**，因此 **this就代表全局对象**。

```javascript
var x = 1;
function test() {
   console.log(this.x);
}
test();  // 1
```
#### 第二种：作为对象方法的调用
函数还可以作为某个对象的方法调用，这时this就指这个**对象**。

```javascript
   function test() {
      console.log(this.x);
    }

    var obj = {};
    obj.x = 1;
    obj.m = test;

    obj.m(); // 1
```

#### 第三种：作为构造函数调用

this就指这个**新对象**。

```javascript
var x = 2;
function test() {
  this.x = 1;
}

var obj = new test();
obj.x // 1

x  // 2，全局变量x的值根本没变
```

#### 第四种：apply、call、bind 调用

```javascript
var x = 0;
function test() {
　console.log(this.x);
}

var obj = {};
obj.x = 1;
obj.m = test;
obj.m.apply() // 0，this指的是全局对象
obj.m.apply(obj); //1，this指的是对象obj
```
- `apply()`的参数为空时，默认调用全局对象（重点）

**call()方法:**
- `函数名称.call(obj,arg1,arg2...argN);`
- 参数说明：
- obj：this要指向的对象,
- arg1,arg2...argN ：参数列表，参数与参数之间使用一个逗号隔开
- 临时改变一次this指向，并立即执行

**apply()方法：**

- `函数名称.apply(obj,[arg1,arg2...,argN]);`
- 参数说明：
- obj：this要指向的对象
- [arg1,arg2...argN]：参数列表，要求格式为数组
- 临时改变一次this指向，并立即执行

**bind方法：**
- `函数名称.bind(obj,arg1,arg2...argN);`
- 参数说明：
- obj：this要指向的对象,
- arg1,arg2...argN ：参数列表，参数与参数之间使用一个逗号隔开（可以分多次传入，call则必须一次性传入所有参数）
- 改变this指向后不会立即执行，返回一个永久改变this指向的函数

**三者的区别：**
- 三者都可以改变函数的this对象指向。
- 三者第一个参数都是 **this要指向的对象**，如果如果没有这个参数或参数为undefined或null，则默认指向全局 **window**。（重点）
- 三者都可以传参，但是apply是数组，而call和bind是参数列表，且apply和call是一次性传入参数，而bind可以分为多次传入。
- bind 是返回绑定this之后的函数，便于稍后调用；apply 、call 则是立即执行，且 **只临时改变一次this指向** 。

使用：

```javascript
var names = 'lisi';
var zs = {names:'zhangsan'};
function f(age){
    console.log(this.names+" is "+age);
}
f(23); // lisi is 23
// 将f函数中的this指向固定到对象zs上；
f.call(zs,32); // zhangsan is 32
f.apply(zs,[23]); // zhangsan is 23
```

### 两种应用情况
#### 第一种：事件绑定中的this

```html
<input type="button" value="按钮" onclick="clickFun()">
<script>
    function clickFun(){
        this // 此函数的运行环境在全局window对象下，因此this指向window;
    }
</script>

<input type="button" value="按钮" onclick="this">
<!-- 运行环境在节点对象中，因此this指向本节点对象 -->
```

- 当事件触发时，属性值就会作为JS代码被执行，当前运行环境下没有clickFun函数，因此浏览器就需要跳出当前运行环境，在整个环境中寻找一个叫clickFun的函数并执行这个函数，所以函数内部的this就指向了全局对象window；
- 如果不是一个函数调用，直接在当前节点对象环境下使用this，那么this就指向当前节点对象。

```html
<input type="button" value="按钮" id="btn">
<script>
    var btn = document.getElementById('btn');
    btn.onclick = function(){
        this ;  // this指向本节点对象
    }
</script>
```
- 为节点对象的属性重新赋值为一个匿名函数，因此函数在执行时就是在节点对象的环境下

> 事件处理的写法：
>
> - html：`<input type="button" value="按钮" onclick="clickFun()">`
> - dom0级：`btn.onclick = function()}`

#### 第二种：window定时器中的this

```javascript
var obj = {
    fun:function(){
        this ;
    }
}

// setInterval()：第一个参数是一个函数或者一段可执行的 JS 代码
setInterval(obj.fun,1000);      // this指向window对象
setInterval('obj.fun()',1000);  // this指向obj对象
```
- 第一个参数`obj.fun`传递的是函数`obj.fun()`的地址，1000毫秒后，函数的运行环境是在Windows下，即this指向window对象
- 第一个参数`'obj.fun()'`传入的一段可执行的 JS 代码，1000毫秒后，通过 obj 对象来找到 fun 函数并调用执行，那么函数的运行环境在对象 obj 内，即this指向obj对象

>setTimeout()：方法只运行一次，延迟时间达到之后会执行一次
>
>setInterval()：循环执行，除非clearInterval()被调用或者窗口被关闭
>
>定时器或者事件监听的使用，一定要清除定时器和销毁事件！！！[window的addEventListener和removeEventListener方法的使用踩雷](https://blog.csdn.net/weixin_43973415/article/details/133361188)

### 应用

tracing添加批量上报的时候有处理过类似问题：

```js
  public updateOptions({
    scheduledDelayMs,
    maxQueueSizeKb,
    enableBatch,
  }: Options) {
    // ...
    if (typeof enableBatch === 'boolean') {
      this._options.enableBatch = enableBatch;
      if (enableBatch) {
        // avoid adding listeners repeatedly, so need to remove listeners firstly if has added previously
        this._adapter.removePageHiddenListener(); // here
        this._adapter.addPageHiddenListener(this.forceFlush.bind(this)); // here
      } else {
        this.onShutdown();
        this.forceFlush();
      }
    }
  }

  public forceFlush() {
    this._curQueueSizeKb = 0;
    this._flushAll();
  }
```



参考博客：[JavaScript 的 this 原理](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html)、[Javascript 的 this 用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)

