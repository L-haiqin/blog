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
- 应用：就是简化`tempalte`里面`{{}}`计算和处理`props`或`$emit`的传值。
- 具有缓存性，页面重新渲染值不变化，计算属性会立即返回之前的计算结果，而不必再次执行函数。

**watch：**
- 是观察的动作。
- 应用：监听`props`，`$emit`或本组件的值执行异步操作。
- 无缓存性，页面重新渲染时值不变化也会执行。

#### 总结 
需要依赖别的属性来动态获得值的时候可以使用`computed`，对于监听到值的变化需要做一些复杂业务逻辑的情况可以使用`watch`。

参考博客：[Vue的computed和watch的细节全面分析](https://segmentfault.com/a/1190000012948175)