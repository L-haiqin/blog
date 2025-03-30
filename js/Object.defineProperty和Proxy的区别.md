> [深入学习Object.defineProperty和Proxy](https://juejin.cn/post/6914109870585151496)

### 1 Object.defineProperty

#### 1.1 用法

`Object.defineProperty(obj, prop, descriptor)`

- obj：要定义属性的对象。
- prop：要定义或修改的属性的名称或 Symbol 。
- descriptor：要定义或修改的属性描述符。

```js
var user = {}
Object.defineProperty(user, 'name', {
  value: 'xyf'
})
console.log(user)

```

#### 1.2 属性描述符

| 名称         | 描述                                                         | 默认值    |
| ------------ | ------------------------------------------------------------ | --------- |
| configurable | 只有该属性的`configurable`为true，该属性的描述符才能够被改变，同时该属性也能从对应的对象上被删除。 | false     |
| enumerable   | 只有该属性的`enumerable`为true，该属性才会出现在对象的枚举属性中。 | false     |
| writable     | 只有该属性的`enumerable`为true，才能被赋值运算符改变。       | false     |
| value        | 该属性对应的值                                               | undefined |
| get          | 属性的getter函数，当访问该属性时，会调用此函数。             | undefined |
| set          | 当属性值被修改时，会调用此函数。该方法接受一个参数，会传入赋值时的 this 对象。 | undefined |

数据描述符：value、writable

存取描述符：get、set

上面两者不能混合使用。

#### 1.3 总结

`Object.defineProperty`能够劫持对象的**属性**，但是需要对对象的每一个属性进行遍历劫持；

如果对象上有新增的属性，则需要对新增的属性再次进行劫持；

如果属性是对象，还需要深度遍历。

`Object.defineProperty`也能够对数组进行劫持，会把数组的索引当作属性来看。

```js
var list = [1,2,3]

list.map((elem, index) => {
    Object.defineProperty(list, index, {
        get: function () {
            console.log("get index:" + index);
            return elem;
        },
        set: function (val) {
            console.log("set index:" + index);
            elem = val;
        }
    });
});

list[2] = 6 
// set index:2

console.log(list[1]) 
// get index:1
// 2
```

为此，Vue的解决方案是劫持`Array.property`原型链上的7个函数，我们通过下面的函数简单进行劫持：

```js
const arratMethods = [
    "push",
    "pop",
    "shift",
    "unshift",
    "splice",
    "sort",
    "reverse",
];

const arrayProto = Object.create(Array.prototype); // 创建一个新的原型对象

arratMethods.forEach((method) => {
    const origin = Array.prototype[method];
    arrayProto[method] = function () {
        console.log("run method", method);
        return origin.apply(this, arguments);
    };
});

const list = [];

list.__proto__ = arrayProto; // 原型链的使用

//run method push
list.push(2);
//run method shift
list.shift(3);
```

### 2 Proxy

`Proxy`是es6新增的一个方法，使用方法如下：

`new Proxy(target, handler);`

- Proxy本身是一个构造函数，通过`new Proxy`生成拦截的实例对象，让外界进行访问；
- `target`：需要代理的目标对象，可以是对象或者数组；
- `handler`：和`Object.defineProperty`中的descriptor描述符有些类似，也是一个对象，用来定制代理规则。

```js
var target = {}

var proxyObj = new Proxy(
    target,
    {
        get: function (target, propKey, receiver) {
            console.log(`getting ${propKey}!`);
            return Reflect.get(target, propKey, receiver);
        },
        set: function (target, propKey, value, receiver) {
            console.log(`setting ${propKey}!`);
            return Reflect.set(target, propKey, value, receiver);
        },
        deleteProperty: function (target, propKey) {
            console.log(`delete ${propKey}!`);
            delete target[propKey];
            return true;
        }
    }
);
//setting count!
proxyObj.count = 1;
//getting count!
//1
console.log(proxyObj.count)
//delete count!
delete proxyObj.count
```

可以看出来`Proxy`是对**整个对象**进行监听拦截，并返回一个新的对象。可以应对删除、新增属性时候，也能做到劫持。

而`Object.defineProperty`只能监听对象的**属性**。

#### 2.1 Reflect

- 内置对象，提供一些像get/delete/set等静态方法，可以直接操作js对象

- https://juejin.cn/post/7333236033038647337

```js
console.log(Reflect.get({'name': 'John'}, 'name')); // Output: John
```

### 3 区别

- **Proxy是对整个对象的代理，而Object.defineProperty只能代理某个属性**。

- 对象上新增属性，Proxy可以监听到，Object.defineProperty不能（可以使用原型链的方式）。

- 数组新增修改，Proxy可以监听到，Object.defineProperty不能（可以使用原型链的方式）。

- 若对象内部属性要全部递归代理，Proxy可以只在调用的时候递归，而Object.definePropery需要一次完成所有递归，性能比Proxy差。

- Proxy不兼容IE，Object.defineProperty不兼容IE8及以下