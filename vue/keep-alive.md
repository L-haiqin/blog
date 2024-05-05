### keep-alive

#### 概念

`keep-alive`用来**缓存组件内部状态，避免重新渲染** 。

keep-alive是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。 

#### 用法

##### 1、在动态组件中的应用

`keep-alive`包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们，此种方式并无太大的实用意义。 

```html
<keep-alive :include="whiteList" :exclude="blackList" :max="amount">
  <component :is="currentComponent"></component>
</keep-alive>
```

##### 2、在vue-router中的应用

使用`keep-alive`可以将所有路径匹配到的路由组件都缓存起来，包括路由组件里面的组件，`keep-alive`大多数使用场景就是这种。

```html
<keep-alive :include="whiteList" :exclude="blackList" :max="amount">
  <router-view></router-view>
</keep-alive>
```

`include`定义缓存白名单，keep-alive会缓存命中的组件；`exclude`定义缓存黑名单，被命中的组件将不会被缓存；`max`定义缓存组件上限，超出上限使用[LRU的策略](https://link.juejin.cn?target=https%3A%2F%2Fbaike.baidu.com%2Fitem%2FLRU)置换缓存数据。

#### 生命周期钩子函数

在被`keep-alive`包含的组件/路由中，会多出两个生命周期的钩子:`activated` 与 `deactivated`。 

##### 1、activated

 **activated在组件第一次渲染时会被调用，之后在每次缓存组件被激活时调用**。 

- 第一次进入缓存组件/路由时，触发的钩子：

mounted=> activated 进入缓存组件 => 执行 beforeRouteEnter回调 

- 再次进入时，不会触发的钩子：

 beforeCreate、 created、 beforeMount、 mounted 

##### 2、deactivated

 **组件被停用(离开路由)时调用** 

使用了`keep-alive`就不会调用`beforeDestroy`(组件销毁前钩子)和`destroyed`(组件销毁)，因为组件没被销毁，被缓存起来了。

这个钩子可以看作`beforeDestroy`的替代，如果你缓存了组件，要在组件销毁的的时候做一些事情，你可以放在这个钩子里。

##### 3、完整顺序

将路由导航、`keep-alive`、和组件生命周期钩子结合起来的，触发顺序，假设是从a组件离开，第一次进入b组件：

1. `beforeRouteLeave`:路由组件的组件离开路由前钩子，可取消路由离开。
2. `beforeEach`: 路由全局前置守卫，可用于登录验证、全局路由loading等。
3. `beforeEnter`: 路由独享守卫
4. `beforeRouteEnter`: 路由组件的组件进入路由前钩子。
5. `beforeResolve`:[路由全局解析守卫](https://link.juejin.cn?target=https%3A%2F%2Frouter.vuejs.org%2Fzh%2Fguide%2Fadvanced%2Fnavigation-guards.html%23%E5%85%A8%E5%B1%80%E8%A7%A3%E6%9E%90%E5%AE%88%E5%8D%AB)
6. `afterEach`:路由全局后置钩子
7. `beforeCreate`:组件生命周期，不能访问`this`。
8. `created`:组件生命周期，可以访问`this`，不能访问dom。
9. `beforeMount`:组件生命周期
10. `deactivated`: 离开缓存组件a，或者触发a的`beforeDestroy`和`destroyed`组件销毁钩子。
11. `mounted`:访问/操作dom。
12. `activated`:进入缓存组件，进入a的嵌套子组件(如果有的话)。
13. 执行beforeRouteEnter回调函数next。

参考博客：[Vue的钩子函数[路由导航守卫、keep-alive、生命周期钩子]](https://juejin.cn/post/6844903641866829838#heading-1)