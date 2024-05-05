#### new创建一个对象的过程

> ```javascript
>  function Father(name) {
>      this.name = name;
>  }
>  let son = new Father('Lisa');
>  console.log(son); //Father {name: "Lisa"}
> ```
>
> - 创建一个空对象 `son {}`
> - 为 `son` 准备原型链连接`son.__proto__ = Father.prototype`
> - 重新绑定`this`，使构造函数的`this`指向新对象`Father.call(this)`
> - 为新对象属性赋值`son.name`
> - 返回this `return this`，此时的新对象就拥有了构造函数的方法和属性了
>
> 例题：
>
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210201180827968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
>
> - new过程的最后一步，如果函数没有返回值，就自动返回这个新对象，这样就可以拥有构造函数的方法和属性

#### new到底做了什么

- 创建一个新的对象；
- 继承父类原型上的方法；
- 添加父类的属性到新的对象上并初始化，保存方法的执行结果；
- 如果执行结果有返回值并且是一个对象，返回执行的结果，否则返回新创建的对象。

#### 手写new

具体解释看[如何手写一个JS中的New方法](https://juejin.cn/post/6844903937405878280)

```javascript
function A() {
    this.a = 1;
};
A.prototype.b = 2;

// 不定参数的写法...arg，是一个数组
function myNew(obj, ...arg) {
    // 基于obj的原型创建一个新的对象
    const newObj = Object.create(obj.prototype);
    
    // 添加属性到新创建的newObj上, 并获取obj函数执行的结果
    const result = obj.apply(newObj, arg);
    
    // 如果执行结果有返回值并且是一个对象, 返回执行的结果, 否则, 返回新创建的对象
    return typeof result === 'object' ? result : newObj;
}

var a1 = myNew(A);
// var a1 = new A(); // 两者效果是一样的

console.log(a1.a) // 1
console.log(a1.b) // 2
```

