#### computed
计算属性，依赖其他属性计算值，并且`computed`的值有缓存，只有当计算值变化才会返回内容。

例子：

```javascript
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
```

#### watch
监听属性，监听到值的变化就会执行回调，在回调中可以进行一些逻辑操作。

例子：

```javascript
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
     firstName: function (val) {
     this.fullName = val + ' ' + this.lastName
  },
  lastName: function (val) {
     this.fullName = this.firstName + ' ' + val
   }
   }
```
- 监听firstName和lastName的变化

#### 区别
**computed：**
- 是计算值。
- 应用：就是简化`tempalte`里面`{{}}`计算和处理`props`或`$emit`的传值。（单向数据流）
- 具有缓存性，页面重新渲染值不变化，计算属性会立即返回之前的计算结果，而不必再次执行函数。
- 监控自己定义的变量，该变量**不在data里面声明**，直接在computed里面定义，然后就可以在页面上进行双向数据绑定展示出结果或者用作其他处理。
- computed主要用于对多个变量或者对象进行处理后返回一个结果值，也就是说**多个变量中的某一个值发生了变化则我们监控的这个值也就会发生变化**，举例：购物车里面的商品列表和总金额之间的关系，只要商品列表里面的商品数量发生变化，或减少或增多或删除商品，总金额都应该发生变化。这里的这个总金额使用computed属性来进行计算是最好的选择

**watch：**

- 是观察的动作。
- 应用：监听`props`，`$emit`或本组件的值执行异步操作。
- 无缓存性，页面重新渲染时值不变化也会执行。
- watch主要用于**监控vue实例的变化**，它监控的变量当然**必须在data里面声明**才可以，它可以监控一个变量，也可以是一个对象 。
- watch一般用于**监控路由、input输入框**的值特殊处理等等，它比较适合的场景是**一个数据影响多个数据**。
- 监控对象属性的写法：` '对象名.属性名'（newVal,oldVal）{} `

#### 总结 
需要依赖别的属性来动态获得值的时候可以使用`computed`，对于监听到值的变化需要做一些复杂业务逻辑的情况可以使用`watch`。

参考博客：[Vue的computed和watch的细节全面分析](https://segmentfault.com/a/1190000012948175)、[Vue中的watch与computed](https://www.cnblogs.com/gunelark/p/8492468.html)