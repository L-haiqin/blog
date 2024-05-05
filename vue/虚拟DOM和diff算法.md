## 虚拟DOM和diff算法

### 虚拟dom

#### 概念

virtual DOM是将真实的DOM的数据抽取出来，以对象的形式模拟树形结构。比如dom是这样的：

```html
<div>
    <p>123</p>
</div>
```

对应的virtual DOM（伪代码）：

```javascript
var Vnode = {
    tag: 'div',
    children: [
        { tag: 'p', text: '123' }
    ]
};
```

在`Javascript`对象中，虚拟`DOM` 表现为一个 `Object`对象。并且最少包含标签名 (`tag`)、属性 (`attrs`) 和子元素对象 (`children`) 三个属性。

#### 为什么

`DOM`是很慢的，其元素非常庞大，页面的性能问题，大部分都是由`DOM`操作引起的。

真实的`DOM`节点，哪怕一个最简单的`div`也包含着很多属性，可以打印出来直观感受一下： 

![img](https://static.vue-js.com/cc95c7f0-442c-11eb-ab90-d9ae814b240d.png)

由此可见，操作`DOM`的代价仍旧是昂贵的，频繁操作还是会出现页面卡顿，影响用户的体验。

**举个例子：**

你用传统的原生`api`或`jQuery`去操作`DOM`时，浏览器会从构建`DOM`树开始从头到尾执行一遍流程。

当你在一次操作时，需要更新10个`DOM`节点，浏览器没这么智能，收到第一个更新`DOM`请求后，并不知道后续还有9次更新操作，因此会马上执行流程，最终执行10次流程。

而通过`VNode`，同样更新10个`DOM`节点，虚拟`DOM`不会立即操作`DOM`，而是将这10次更新的`diff`内容保存到本地的一个`js`对象中，最终将这个`js`对象一次性`attach`到`DOM`树上，避免大量的无谓计算。

> 很多人认为虚拟 DOM 最大的优势是 **diff 算法**，**减少 JavaScript 操作真实 DOM 的带来的性能消耗**。虽然这一个虚拟 DOM 带来的一个优势，但并不是全部。
>
> 虚拟 DOM 最大的优势在于**抽象了原本的渲染过程，实现了跨平台的能力**，而不仅仅局限于浏览器的 DOM，可以是安卓和 IOS 的原生组件，可以是近期很火热的小程序，也可以是各种GUI

#### 实现原理



### diff算法

#### 比较方式

`diff`整体策略为：深度优先，同层比较

- 比较只会在同层级进行, 不会跨层级比较

![img](https://static001.infoq.cn/resource/image/91/54/91e9c9519a11caa0c5bf70714383f054.png)

- 比较的过程中，循环从两边向中间收拢

![img](https://static001.infoq.cn/resource/image/2d/ec/2dcd6ad5cf82c65b9cfc43a27ba1e4ec.png)

#### 当数据发生变化时，vue是怎么更新节点的？

根据真实DOM生成一颗`virtual DOM`，当`virtual DOM`某个节点的数据改变后会生成一个新的`Vnode`，然后`Vnode`和`oldVnode`作对比，发现有不一样的地方就直接修改在真实的DOM上，然后使`oldVnode`的值为`Vnode`。

diff的过程就是调用名为`patch`的函数，比较新旧节点，一边比较一边给**真实的DOM**打补丁。

在采取diff算法比较新旧节点的时候，比较只会在**同层级**进行, 不会跨层级比较。

#### 实现原理

当数据发生改变时，`set`方法会调用`Dep.notify`通知所有订阅者`Watcher`，订阅者就会调用`patch`给真实的`DOM`打补丁，更新相应的视图。