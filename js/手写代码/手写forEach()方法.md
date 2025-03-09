### 原生js实现forEach()方法

`forEach()`接受两个参数，分别是：每一项上运行的函数 和 运行该函数的作用域对象（即影响this的值，可选）。没有返回值。

```javascript
arr.forEach(function(item, index, array) {
    //执行某些操作
});
```

模拟如下：

循环调用第一个参数fn()。

```javascript
Array.prototype.forEach = function(fn, context) {
    // console.log('arguments:', arguments)
    // console.log('this:', this) // [1,2,3,4,5]
    
    // var context = arguments[1]; // 获取第二个参数context
    if(typeof fn !== "function") {
        throw new TypeError(fn + "is not a function");
    }

    for(var i = 0; i < this.length; i++) {
        fn.call(context, this[i], i, this); // fn的this指向context，并传入参数列表(item, index, array)，括号里面的参数this表示的是数组
    }
};

var arr = [1, 2, 3, 4, 5];
arr.forEach( item => {
    console.log(item);
});
```

原型链中 this 的指向：

- this这个值在一个继承机制中，仍然是指向它原本属于的对象，而不是从原型链上找到它时，它所属于的对象。

