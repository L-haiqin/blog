### cvte笔试

#### 选择题

1、浏览器缓存

2、下列输出的是（）。

```javascript
function fn1(){}
function fn2(){}
fn1.prototype = new fn2();
fn2.prototype = new fn1();
const f1 = new fn1();
const f2 = new fn2();
console.log(f1.constructor, f2.constructor)
```

3、事件冒泡、事件捕获选择打印顺序题

4、下列输出的是（10, 11, 3）。

```javascript
var a = 10, b = 11, c = 12;
function test(a){
    a = 1;
    var b = 2;
    c = 3;
}
test(10)
console.log(a)
console.log(b)
console.log(c)
```

5、http2.0的特点：多路复用、二进制分祯、头部压缩、支持长链接

6、原型链

7、简单请求的header字段和方法

8、下列输出的是（cvte 1 2020）。

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
```

9、下列输出的是（cvte 1 2020）。

```javascript
var ctx = {_val: 30};
var _val = 10;
function rootFn(){
    console.log(this._val);
    this._val = 20;
    function childFn(){
        console.log(this._val);
    }
    return childFn();
}
rootFn.apply(ctx)();
console.log(ctx._val);
```

10、CSRF、XSS概念相关

11、在node环境下，执行`node test.js one two=three four`，`console.log('arg: ', process.argv[3]);`打印输出的结果为（）。

12、以下哪些方法能枚举对象自身的属性（）。

A：for in

B：in关键字

C：object.entries()

D：hasOwnProperty()

13、css设置链接样式的先后顺序：`:link > :visited > :hover > :active`。

14、重排（回流）重绘，`visibility: hidden;`和`display: none`分别触发的是：（重绘）和（重绘、重排）。

15、`e.preventDefault()`可取消事件的默认动作，`e.stopPropagation()`可阻止事件冒泡补课阻止事件捕获，这两句话对否

16、在node环境下，下列输出的是（）。

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

#### 编程题

1、驼峰命名法

> camelCase('Foo Bar')  // fooBar
>
> camelCase('--foo-Bar--')  // fooBar

```javascript
function camelCase(s){
    // 补充代码
}
```

2、用递归实现一个函数buildTree()，该函数可以生成一个n层的完全二叉树，二叉树节点的值为当前层级（根结点为0）。

生成完二叉树后，实现levelPrint方法层序遍历将二叉树输出。

```javascript
function node(val, left, right){
    this.val = val;
    this.left = left;
    this.right = right;
}

const root = new node(0,null,null);
let totalLevel = 4;
buildTree(root, totalLevel);
levelPrint(root); // [[0],[1,1],[2,2,2,2],[3,3,3,3,3,3]]
```

