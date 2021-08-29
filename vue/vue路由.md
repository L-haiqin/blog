### vue路由

#### 1、router-link

**router-link** 是一个组件，它默认会被渲染成一个带有链接的a标签，通过to属性指定链接地址。
 注意：被选中的router-link将自动添加一个class属性值`.router-link-active`。

```html
<router-link to="/">导航名称</router-link>
```

#### 2、router-view 

用于渲染匹配到的组件，可以给router-view组件设置**transition过渡**。

```html
<transition name="fade">
  <router-view ></router-view>
</transition>
```

#### 3、动态路由匹配

把某种模式匹配到的所有路由，全都映射到同个组件，这时候需要用到动态路由。

比如，我们有一个 User 组件，对于所有 ID 各不相同的用户，都要使用这个组件来渲染。

##### 1）router.map()

调用router的map方法映射路由，每条路由以key-value的形式存在，key是路径，value是组件。

例如：'/home'是一条路由的key，它表示路径；{component: Home}则表示该条路由映射的组件：

```javascript
router.map({
    '/home': { component: Home },
    '/about': { component: About }
})
```

##### 2）动态路径参数

```java
const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]

})
```

- 使用冒号` : `标记。

- 当匹配到一个路由时，参数值会被设置到**this.$route.params**，可以在每个组件内使用。
- 还可以食用正则表达式。

#### 4、响应路由参数的变化

复用组件时，想对路由参数的变化作出响应的话:

##### 1）watch（监测变化） $route 对象：

```javascript
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```

##### 2）beforeRouteUpdate 导航守卫
 如果目的地和当前路由相同，只有参数发生了改变 (比如从一个用户资料到另一个 `/users/1` -> `/users/2`)，你需要使用 `beforeRouteUpdate`来响应这个变化 (比如抓取用户信息)。

```javascript
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```

#### 5、重定向

刚进入应用都是进入到“/”这个路由的，如果想直接进入到“/goods”怎么办，有两种方法。一种是利用重定向，另一种是利用vue-router的导航式编程。

redirect基本重定向：

```java
const routes = [
  { path: '/', redirect: '/goods'}
]
// 重定向的目标也可以是一个命名的路由。
const routes = [
  { path: '/', redirect: { name: 'goods' }}
]
// 重定向时也可以传递参数
{
  path:'/',
  redirect:'/goods/:newsId(\\d+)/:userName'
}
```

#### 6、导航式编程

`this.$router.push()`

`this.$router.replace()`

`this.$router.go()`

#### 7、钩子函数（导航守卫）

全局钩子： beforeEach、 afterEach、beforeResolve

单个路由里面的钩子：beforeEnter

组件路由：beforeRouteEnter、 beforeRouteUpdate、 beforeRouteLeave

##### 1）全局守卫

**无论访问哪一个路径，都会触发全局的钩子函数，位置是调用router实例的方法**

- 全局前置守卫`router.beforeEach() `进入之前触发

- 全局后置守卫`router.afterEach() `进入之后触发

![img](https://upload-images.jianshu.io/upload_images/14942208-147d403d5e50196b.png?imageMogr2/auto-orient/strip|imageView2/2/w/312)

接受三个参数：

- to:路由将要跳转的路径信息，信息是包含在对像里边的。
- from:路径跳转前的路径信息，也是一个对象的形式。
- next:路由的控制参数，常用的有next(true)和next(false)。

##### 2）单个路由独享的守卫

写在路由配置中。

![img](https://upload-images.jianshu.io/upload_images/14942208-7e61134f639d3991.png?imageMogr2/auto-orient/strip|imageView2/2/w/326)

##### 3）组件级路由钩子

写在组件中。

![img](https://upload-images.jianshu.io/upload_images/14942208-ce6e88dc632eedf7.png?imageMogr2/auto-orient/strip|imageView2/2/w/633)

#### 8、完整的导航解析流程

1）导航被触发。

2）在失活的组件里调用离开守卫。

3）调用全局的 beforeEach 守卫。

4）在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。

5）在路由配置里调用 beforeEnter。

6）解析异步路由组件。

7）在被激活的组件里调用 beforeRouteEnter。

8）调用全局的 beforeResolve 守卫 (2.5+)。

9）导航被确认。

10）调用全局的 afterEach 钩子。

11）触发 DOM 更新。

12）用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。

参考博客：[Vue-Router路由钩子函数(导航守卫)](https://www.jianshu.com/p/ddcb7ba28c5e)、[vue-router（路由）详细教程](https://blog.csdn.net/wulala_hei/article/details/80488727)

#### 9、路由嵌套

配置文件：

![img](https://upload-images.jianshu.io/upload_images/14561295-f4611fa16d2aa7d5.png?imageMogr2/auto-orient/strip|imageView2/2/w/473)

- 使用`children`属性实现子路由时，子路由`path`前面 不能带`/`，以`/` 开头的嵌套路径会被当作根路径。

在父组件中引入：

![img](https://upload-images.jianshu.io/upload_images/14561295-6e28cb037d96c56c.png?imageMogr2/auto-orient/strip|imageView2/2/w/763)

#### 10、mode模式

##### 1）hash模式

默认。

##### 1）history模式

利用 `history.pushState` API 来完成 URL 跳转而无须重新加载页面。